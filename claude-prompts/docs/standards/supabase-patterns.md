# Supabase Patterns & Best Practices

## 🗄️ Database Design Patterns

Comprehensive patterns for Supabase database design, authentication, and real-time features optimized for AI code generation.

## 🏗️ Schema Design Patterns

### 1. User Management Schema
```sql
-- Enable necessary extensions
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "pgcrypto";

-- Custom types
CREATE TYPE user_role AS ENUM ('admin', 'user', 'moderator');
CREATE TYPE subscription_status AS ENUM ('active', 'inactive', 'cancelled', 'past_due');

-- Core user profiles table
CREATE TABLE profiles (
  id UUID REFERENCES auth.users(id) PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  name TEXT,
  avatar_url TEXT,
  role user_role DEFAULT 'user',
  subscription_status subscription_status DEFAULT 'inactive',
  subscription_id TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- User preferences
CREATE TABLE user_preferences (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
  theme TEXT DEFAULT 'system',
  language TEXT DEFAULT 'en',
  notifications JSONB DEFAULT '{"email": true, "push": true, "sms": false}',
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  
  UNIQUE(user_id)
);

-- Organizations/Teams
CREATE TABLE organizations (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  name TEXT NOT NULL,
  slug TEXT UNIQUE NOT NULL,
  description TEXT,
  avatar_url TEXT,
  owner_id UUID REFERENCES profiles(id) NOT NULL,
  subscription_id TEXT,
  settings JSONB DEFAULT '{}',
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Organization memberships
CREATE TABLE organization_members (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  organization_id UUID REFERENCES organizations(id) ON DELETE CASCADE,
  user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
  role TEXT DEFAULT 'member' CHECK (role IN ('owner', 'admin', 'member')),
  invited_by UUID REFERENCES profiles(id),
  joined_at TIMESTAMPTZ DEFAULT NOW(),
  
  UNIQUE(organization_id, user_id)
);

-- Organization invitations
CREATE TABLE organization_invitations (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  organization_id UUID REFERENCES organizations(id) ON DELETE CASCADE,
  email TEXT NOT NULL,
  role TEXT DEFAULT 'member' CHECK (role IN ('admin', 'member')),
  invited_by UUID REFERENCES profiles(id) NOT NULL,
  expires_at TIMESTAMPTZ NOT NULL DEFAULT (NOW() + INTERVAL '7 days'),
  accepted_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  
  UNIQUE(organization_id, email)
);
```

### 2. Content Management Schema
```sql
-- Categories for content organization
CREATE TABLE categories (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  name TEXT NOT NULL,
  slug TEXT UNIQUE NOT NULL,
  description TEXT,
  parent_id UUID REFERENCES categories(id),
  sort_order INTEGER DEFAULT 0,
  is_active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Main content table (posts, articles, pages)
CREATE TABLE content (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  title TEXT NOT NULL,
  slug TEXT NOT NULL,
  excerpt TEXT,
  body TEXT,
  featured_image TEXT,
  content_type TEXT DEFAULT 'post' CHECK (content_type IN ('post', 'page', 'article')),
  status TEXT DEFAULT 'draft' CHECK (status IN ('draft', 'published', 'archived')),
  author_id UUID REFERENCES profiles(id) NOT NULL,
  category_id UUID REFERENCES categories(id),
  tags TEXT[],
  meta_title TEXT,
  meta_description TEXT,
  published_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  
  UNIQUE(slug, content_type)
);

-- Content revisions for version control
CREATE TABLE content_revisions (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  content_id UUID REFERENCES content(id) ON DELETE CASCADE,
  title TEXT NOT NULL,
  body TEXT,
  author_id UUID REFERENCES profiles(id) NOT NULL,
  revision_number INTEGER NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Comments system
CREATE TABLE comments (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  content_id UUID REFERENCES content(id) ON DELETE CASCADE,
  parent_id UUID REFERENCES comments(id),
  author_id UUID REFERENCES profiles(id),
  author_name TEXT,
  author_email TEXT,
  body TEXT NOT NULL,
  status TEXT DEFAULT 'pending' CHECK (status IN ('pending', 'approved', 'spam', 'rejected')),
  ip_address INET,
  user_agent TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

### 3. E-commerce Schema
```sql
-- Products
CREATE TABLE products (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  name TEXT NOT NULL,
  slug TEXT UNIQUE NOT NULL,
  description TEXT,
  short_description TEXT,
  sku TEXT UNIQUE,
  price DECIMAL(10,2) NOT NULL,
  compare_price DECIMAL(10,2),
  cost_price DECIMAL(10,2),
  track_inventory BOOLEAN DEFAULT TRUE,
  inventory_quantity INTEGER DEFAULT 0,
  allow_backorders BOOLEAN DEFAULT FALSE,
  weight DECIMAL(8,2),
  dimensions JSONB, -- {length, width, height}
  category_id UUID REFERENCES categories(id),
  tags TEXT[],
  images TEXT[],
  status TEXT DEFAULT 'active' CHECK (status IN ('active', 'inactive', 'archived')),
  seo_title TEXT,
  seo_description TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Product variants
CREATE TABLE product_variants (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  product_id UUID REFERENCES products(id) ON DELETE CASCADE,
  name TEXT NOT NULL,
  sku TEXT UNIQUE,
  price DECIMAL(10,2),
  compare_price DECIMAL(10,2),
  cost_price DECIMAL(10,2),
  inventory_quantity INTEGER DEFAULT 0,
  weight DECIMAL(8,2),
  image_url TEXT,
  options JSONB, -- {size: "L", color: "red"}
  position INTEGER DEFAULT 0,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Orders
CREATE TABLE orders (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  order_number TEXT UNIQUE NOT NULL,
  user_id UUID REFERENCES profiles(id),
  email TEXT NOT NULL,
  phone TEXT,
  status TEXT DEFAULT 'pending' CHECK (status IN ('pending', 'confirmed', 'processing', 'shipped', 'delivered', 'cancelled', 'refunded')),
  financial_status TEXT DEFAULT 'pending' CHECK (financial_status IN ('pending', 'paid', 'partially_paid', 'refunded', 'partially_refunded')),
  subtotal DECIMAL(10,2) NOT NULL,
  tax_amount DECIMAL(10,2) DEFAULT 0,
  shipping_amount DECIMAL(10,2) DEFAULT 0,
  discount_amount DECIMAL(10,2) DEFAULT 0,
  total_amount DECIMAL(10,2) NOT NULL,
  currency TEXT DEFAULT 'USD',
  payment_method TEXT,
  payment_id TEXT,
  shipping_address JSONB NOT NULL,
  billing_address JSONB,
  notes TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Order items
CREATE TABLE order_items (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  order_id UUID REFERENCES orders(id) ON DELETE CASCADE,
  product_id UUID REFERENCES products(id),
  variant_id UUID REFERENCES product_variants(id),
  product_name TEXT NOT NULL,
  variant_name TEXT,
  sku TEXT,
  quantity INTEGER NOT NULL CHECK (quantity > 0),
  price DECIMAL(10,2) NOT NULL,
  total_price DECIMAL(10,2) NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

## 🔒 Row Level Security (RLS) Patterns

### 1. User Profile Security
```sql
-- Enable RLS on all tables
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE user_preferences ENABLE ROW LEVEL SECURITY;
ALTER TABLE organizations ENABLE ROW LEVEL SECURITY;
ALTER TABLE organization_members ENABLE ROW LEVEL SECURITY;

-- Users can view and update their own profile
CREATE POLICY "Users can view own profile" ON profiles
  FOR SELECT USING (auth.uid() = id);

CREATE POLICY "Users can update own profile" ON profiles
  FOR UPDATE USING (auth.uid() = id);

-- Users can view and update their own preferences
CREATE POLICY "Users can manage own preferences" ON user_preferences
  FOR ALL USING (auth.uid() = user_id);

-- Organization access policies
CREATE POLICY "Organization members can view organization" ON organizations
  FOR SELECT USING (
    id IN (
      SELECT organization_id FROM organization_members 
      WHERE user_id = auth.uid()
    )
  );

CREATE POLICY "Organization owners can update organization" ON organizations
  FOR UPDATE USING (owner_id = auth.uid());

-- Organization membership policies
CREATE POLICY "Organization members can view memberships" ON organization_members
  FOR SELECT USING (
    organization_id IN (
      SELECT organization_id FROM organization_members 
      WHERE user_id = auth.uid()
    )
  );

CREATE POLICY "Organization admins can manage memberships" ON organization_members
  FOR ALL USING (
    organization_id IN (
      SELECT organization_id FROM organization_members 
      WHERE user_id = auth.uid() 
      AND role IN ('owner', 'admin')
    )
  );
```

### 2. Content Security
```sql
-- Content visibility policies
CREATE POLICY "Anyone can view published content" ON content
  FOR SELECT USING (
    status = 'published' 
    AND (published_at IS NULL OR published_at <= NOW())
  );

CREATE POLICY "Authors can view their own content" ON content
  FOR SELECT USING (author_id = auth.uid());

CREATE POLICY "Authors can manage their own content" ON content
  FOR ALL USING (author_id = auth.uid());

-- Admin access to all content
CREATE POLICY "Admins can manage all content" ON content
  FOR ALL USING (
    auth.uid() IN (
      SELECT id FROM profiles WHERE role = 'admin'
    )
  );

-- Comments policies
CREATE POLICY "Anyone can view approved comments" ON comments
  FOR SELECT USING (status = 'approved');

CREATE POLICY "Authenticated users can create comments" ON comments
  FOR INSERT WITH CHECK (
    auth.uid() IS NOT NULL
    AND author_id = auth.uid()
  );

CREATE POLICY "Users can edit their own comments" ON comments
  FOR UPDATE USING (
    author_id = auth.uid()
    AND created_at > NOW() - INTERVAL '15 minutes'
  );
```

### 3. E-commerce Security
```sql
-- Product visibility (public)
CREATE POLICY "Anyone can view active products" ON products
  FOR SELECT USING (status = 'active');

-- Admin-only product management
CREATE POLICY "Admins can manage products" ON products
  FOR ALL USING (
    auth.uid() IN (
      SELECT id FROM profiles WHERE role IN ('admin')
    )
  );

-- Order access policies
CREATE POLICY "Users can view their own orders" ON orders
  FOR SELECT USING (user_id = auth.uid());

CREATE POLICY "Users can create orders" ON orders
  FOR INSERT WITH CHECK (user_id = auth.uid());

CREATE POLICY "Admins can view all orders" ON orders
  FOR SELECT USING (
    auth.uid() IN (
      SELECT id FROM profiles WHERE role IN ('admin')
    )
  );

-- Order items follow order permissions
CREATE POLICY "Users can view their order items" ON order_items
  FOR SELECT USING (
    order_id IN (
      SELECT id FROM orders WHERE user_id = auth.uid()
    )
  );
```

## 📡 Real-time Patterns

### 1. Real-time Subscriptions Setup
```typescript
// lib/supabase-realtime.ts
import { createClientComponentClient } from '@supabase/auth-helpers-nextjs'
import type { Database } from '@/types/supabase'

export class RealtimeService {
  private supabase = createClientComponentClient<Database>()
  
  // Subscribe to user's orders
  subscribeToUserOrders(userId: string, callback: (payload: any) => void) {
    return this.supabase
      .channel('user_orders')
      .on(
        'postgres_changes',
        {
          event: '*',
          schema: 'public',
          table: 'orders',
          filter: `user_id=eq.${userId}`
        },
        callback
      )
      .subscribe()
  }
  
  // Subscribe to organization activity
  subscribeToOrganization(orgId: string, callback: (payload: any) => void) {
    return this.supabase
      .channel(`org_${orgId}`)
      .on(
        'postgres_changes',
        {
          event: '*',
          schema: 'public',
          table: 'organization_members',
          filter: `organization_id=eq.${orgId}`
        },
        callback
      )
      .subscribe()
  }
  
  // Subscribe to comments on content
  subscribeToComments(contentId: string, callback: (payload: any) => void) {
    return this.supabase
      .channel(`comments_${contentId}`)
      .on(
        'postgres_changes',
        {
          event: 'INSERT',
          schema: 'public',
          table: 'comments',
          filter: `content_id=eq.${contentId}`
        },
        callback
      )
      .subscribe()
  }
  
  // Presence for collaborative features
  subscribeToPresence(channelName: string, user: { id: string, name: string }) {
    const channel = this.supabase.channel(channelName)
    
    return channel
      .on('presence', { event: 'sync' }, () => {
        const newState = channel.presenceState()
        console.log('Presence sync:', newState)
      })
      .on('presence', { event: 'join' }, ({ key, newPresences }) => {
        console.log('User joined:', key, newPresences)
      })
      .on('presence', { event: 'leave' }, ({ key, leftPresences }) => {
        console.log('User left:', key, leftPresences)
      })
      .subscribe(async (status) => {
        if (status !== 'SUBSCRIBED') return
        
        await channel.track({
          user_id: user.id,
          name: user.name,
          online_at: new Date().toISOString(),
        })
      })
  }
}

export const realtimeService = new RealtimeService()
```

### 2. React Hooks for Real-time Data
```typescript
// hooks/use-realtime-orders.ts
'use client'

import { useEffect, useState } from 'react'
import { realtimeService } from '@/lib/supabase-realtime'
import type { Order } from '@/types/orders'

export function useRealtimeOrders(userId: string, initialOrders: Order[] = []) {
  const [orders, setOrders] = useState<Order[]>(initialOrders)
  
  useEffect(() => {
    const channel = realtimeService.subscribeToUserOrders(userId, (payload) => {
      const { eventType, new: newRecord, old: oldRecord } = payload
      
      setOrders((current) => {
        switch (eventType) {
          case 'INSERT':
            return [newRecord, ...current]
          
          case 'UPDATE':
            return current.map((order) =>
              order.id === newRecord.id ? newRecord : order
            )
          
          case 'DELETE':
            return current.filter((order) => order.id !== oldRecord.id)
          
          default:
            return current
        }
      })
    })
    
    return () => {
      channel.unsubscribe()
    }
  }, [userId])
  
  return orders
}
```

```typescript
// hooks/use-realtime-comments.ts
'use client'

import { useEffect, useState } from 'react'
import { realtimeService } from '@/lib/supabase-realtime'
import type { Comment } from '@/types/comments'

export function useRealtimeComments(contentId: string, initialComments: Comment[] = []) {
  const [comments, setComments] = useState<Comment[]>(initialComments)
  
  useEffect(() => {
    const channel = realtimeService.subscribeToComments(contentId, (payload) => {
      if (payload.eventType === 'INSERT') {
        const newComment = payload.new as Comment
        
        // Only add approved comments to the UI
        if (newComment.status === 'approved') {
          setComments((current) => [...current, newComment])
        }
      }
    })
    
    return () => {
      channel.unsubscribe()
    }
  }, [contentId])
  
  return comments
}
```

## 🔐 Authentication Patterns

### 1. Authentication Service
```typescript
// services/auth-service.ts
import { createClientComponentClient } from '@supabase/auth-helpers-nextjs'
import type { Database } from '@/types/supabase'

export class AuthService {
  private supabase = createClientComponentClient<Database>()
  
  async signUp(email: string, password: string, userData?: any) {
    const { data, error } = await this.supabase.auth.signUp({
      email,
      password,
      options: {
        data: userData,
        emailRedirectTo: `${location.origin}/auth/callback`,
      },
    })
    
    if (error) throw error
    return data
  }
  
  async signIn(email: string, password: string) {
    const { data, error } = await this.supabase.auth.signInWithPassword({
      email,
      password,
    })
    
    if (error) throw error
    return data
  }
  
  async signInWithProvider(provider: 'google' | 'github' | 'discord') {
    const { data, error } = await this.supabase.auth.signInWithOAuth({
      provider,
      options: {
        redirectTo: `${location.origin}/auth/callback`,
      },
    })
    
    if (error) throw error
    return data
  }
  
  async signOut() {
    const { error } = await this.supabase.auth.signOut()
    if (error) throw error
  }
  
  async resetPassword(email: string) {
    const { data, error } = await this.supabase.auth.resetPasswordForEmail(email, {
      redirectTo: `${location.origin}/auth/reset-password`,
    })
    
    if (error) throw error
    return data
  }
  
  async updatePassword(password: string) {
    const { data, error } = await this.supabase.auth.updateUser({
      password
    })
    
    if (error) throw error
    return data
  }
  
  async updateProfile(updates: { name?: string; avatar_url?: string }) {
    const { data: { user }, error: userError } = await this.supabase.auth.getUser()
    if (userError || !user) throw userError || new Error('Not authenticated')
    
    const { data, error } = await this.supabase
      .from('profiles')
      .update(updates)
      .eq('id', user.id)
      .select()
      .single()
    
    if (error) throw error
    return data
  }
  
  async getProfile() {
    const { data: { user }, error: userError } = await this.supabase.auth.getUser()
    if (userError || !user) throw userError || new Error('Not authenticated')
    
    const { data, error } = await this.supabase
      .from('profiles')
      .select('*')
      .eq('id', user.id)
      .single()
    
    if (error) throw error
    return data
  }
}

export const authService = new AuthService()
```

### 2. Zustand Auth Store
```typescript
// stores/auth-store.ts
import { create } from 'zustand'
import { persist } from 'zustand/middleware'
import { authService } from '@/services/auth-service'
import type { User } from '@supabase/auth-helpers-nextjs'
import type { Profile } from '@/types/profiles'

interface AuthState {
  user: User | null
  profile: Profile | null
  loading: boolean
  
  // Actions
  signIn: (email: string, password: string) => Promise<void>
  signUp: (email: string, password: string, userData?: any) => Promise<void>
  signOut: () => Promise<void>
  updateProfile: (updates: Partial<Profile>) => Promise<void>
  initialize: () => Promise<void>
  setUser: (user: User | null) => void
  setProfile: (profile: Profile | null) => void
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set, get) => ({
      user: null,
      profile: null,
      loading: true,
      
      signIn: async (email: string, password: string) => {
        try {
          const { user } = await authService.signIn(email, password)
          if (user) {
            const profile = await authService.getProfile()
            set({ user, profile })
          }
        } catch (error) {
          console.error('Sign in error:', error)
          throw error
        }
      },
      
      signUp: async (email: string, password: string, userData?: any) => {
        try {
          await authService.signUp(email, password, userData)
          // Note: User will be set via auth state change after email confirmation
        } catch (error) {
          console.error('Sign up error:', error)
          throw error
        }
      },
      
      signOut: async () => {
        try {
          await authService.signOut()
          set({ user: null, profile: null })
        } catch (error) {
          console.error('Sign out error:', error)
          throw error
        }
      },
      
      updateProfile: async (updates: Partial<Profile>) => {
        try {
          const updatedProfile = await authService.updateProfile(updates)
          set({ profile: updatedProfile })
        } catch (error) {
          console.error('Update profile error:', error)
          throw error
        }
      },
      
      initialize: async () => {
        try {
          set({ loading: true })
          
          // Get current user
          const { data: { user } } = await authService.supabase.auth.getUser()
          
          if (user) {
            const profile = await authService.getProfile()
            set({ user, profile, loading: false })
          } else {
            set({ user: null, profile: null, loading: false })
          }
          
          // Listen for auth changes
          authService.supabase.auth.onAuthStateChange(async (event, session) => {
            if (session?.user) {
              const profile = await authService.getProfile()
              set({ user: session.user, profile, loading: false })
            } else {
              set({ user: null, profile: null, loading: false })
            }
          })
        } catch (error) {
          console.error('Auth initialization error:', error)
          set({ loading: false })
        }
      },
      
      setUser: (user) => set({ user }),
      setProfile: (profile) => set({ profile }),
    }),
    {
      name: 'auth-store',
      partialize: (state) => ({ 
        user: state.user,
        profile: state.profile 
      }),
    }
  )
)
```

## 📊 Database Functions & Triggers

### 1. Utility Functions
```sql
-- Function to update updated_at timestamp
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Apply to all tables with updated_at column
CREATE TRIGGER update_profiles_updated_at
  BEFORE UPDATE ON profiles
  FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_organizations_updated_at
  BEFORE UPDATE ON organizations
  FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_content_updated_at
  BEFORE UPDATE ON content
  FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
```

### 2. Business Logic Functions
```sql
-- Function to handle new user signup
CREATE OR REPLACE FUNCTION handle_new_user()
RETURNS TRIGGER AS $$
BEGIN
  INSERT INTO profiles (id, email, name)
  VALUES (
    NEW.id,
    NEW.email,
    COALESCE(NEW.raw_user_meta_data->>'name', NEW.raw_user_meta_data->>'full_name')
  );
  
  -- Create default preferences
  INSERT INTO user_preferences (user_id)
  VALUES (NEW.id);
  
  RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

CREATE TRIGGER on_auth_user_created
  AFTER INSERT ON auth.users
  FOR EACH ROW EXECUTE FUNCTION handle_new_user();

-- Function to create order number
CREATE OR REPLACE FUNCTION generate_order_number()
RETURNS TEXT AS $$
DECLARE
  order_num TEXT;
BEGIN
  SELECT 'ORD-' || EXTRACT(YEAR FROM NOW()) || '-' || 
         LPAD(NEXTVAL('order_number_seq')::TEXT, 6, '0')
  INTO order_num;
  
  RETURN order_num;
END;
$$ LANGUAGE plpgsql;

-- Create sequence for order numbers
CREATE SEQUENCE IF NOT EXISTS order_number_seq START 1;

-- Function to automatically set order number
CREATE OR REPLACE FUNCTION set_order_number()
RETURNS TRIGGER AS $$
BEGIN
  IF NEW.order_number IS NULL THEN
    NEW.order_number = generate_order_number();
  END IF;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER set_order_number_trigger
  BEFORE INSERT ON orders
  FOR EACH ROW EXECUTE FUNCTION set_order_number();
```

### 3. Analytics Functions
```sql
-- Function to get user statistics
CREATE OR REPLACE FUNCTION get_user_stats(user_uuid UUID)
RETURNS JSON AS $$
DECLARE
  result JSON;
BEGIN
  SELECT json_build_object(
    'total_orders', COALESCE(order_count.count, 0),
    'total_spent', COALESCE(order_sum.sum, 0),
    'avg_order_value', COALESCE(order_sum.sum / order_count.count, 0),
    'last_order_date', last_order.created_at
  ) INTO result
  FROM (
    SELECT COUNT(*) as count
    FROM orders 
    WHERE user_id = user_uuid AND status != 'cancelled'
  ) order_count
  CROSS JOIN (
    SELECT SUM(total_amount) as sum
    FROM orders 
    WHERE user_id = user_uuid AND status != 'cancelled'
  ) order_sum
  CROSS JOIN (
    SELECT created_at
    FROM orders 
    WHERE user_id = user_uuid AND status != 'cancelled'
    ORDER BY created_at DESC
    LIMIT 1
  ) last_order;
  
  RETURN result;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Function to get organization statistics
CREATE OR REPLACE FUNCTION get_organization_stats(org_uuid UUID)
RETURNS JSON AS $$
DECLARE
  result JSON;
BEGIN
  SELECT json_build_object(
    'member_count', member_stats.count,
    'content_count', content_stats.count,
    'created_at', org.created_at
  ) INTO result
  FROM organizations org
  CROSS JOIN (
    SELECT COUNT(*) as count
    FROM organization_members 
    WHERE organization_id = org_uuid
  ) member_stats
  CROSS JOIN (
    SELECT COUNT(*) as count
    FROM content 
    WHERE author_id IN (
      SELECT user_id FROM organization_members WHERE organization_id = org_uuid
    )
  ) content_stats
  WHERE org.id = org_uuid;
  
  RETURN result;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

---

*These Supabase patterns provide a robust foundation for scalable, secure, and feature-rich applications with real-time capabilities.*