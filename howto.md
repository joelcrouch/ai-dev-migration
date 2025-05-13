# HowTo.md: Building an AI-Powered Code Migration Assistant

This document chronicles our development journey, capturing lessons learned, challenges overcome, and best practices discovered while building the AI-Powered Code Migration Assistant.

## Project Setup

### Day 1: Initial Setup and Configuration

#### What Went Well

1. **Project Structure Planning**: Taking time to thoroughly outline the API structure before writing any code helped establish a clear vision for the system architecture.

2. **Technology Selection**: Choosing Next.js for the frontend provides several advantages:

   - Server-side rendering improves initial load performance
   - API routes simplify backend connectivity
   - File-based routing reduces boilerplate code
   - Plus gave me a complex project to learn Next.js

3. **Documentation First Approach**: Creating a comprehensive README.md before implementation helps maintain focus on project goals and requirements.

4. **WHERE TO DEVELOP**
   I generally like to spin up linux vm's for development purposes, for reasons like: not cluttering my host, native support for lots of tools, windows is 'sometimes' 'picky' to develop with, and I can make a change, break the whole system, and just roll it back. However, I do write reamdme's and howto's on any old device I am working on. So learn how to spin up VM's quickly, and maybe even automate it. I think vagrant or ansible or docker can be your friend in this instance.

#### Challenges & Solutions

1. **Challenge**: Deciding on the appropriate database structure for storing migration templates and history.

   **Solution**: We chose MongoDB for its flexibility with document structures, which is ideal for storing code snippets and migration metadata that might vary between different framework migrations. A regular SQL db might be less suitable, since the values (code) being passed don't necessarily line up to a 'normal' key:value pair like we might see in a routine SQL db. Also I think under the hood, implementations are different and Mongodb is more suited for sending data to/from ML/LL models.

- MongoDB is a NoSQL database, which stores data in a document-oriented format (using BSON, which is similar to JSON). This structure allows for more flexibility in how data is stored compared to a traditional SQL database that uses fixed tables and schemas.

- In this case, since migration data might vary significantly based on the source and target frameworks, MongoDB’s schema-less nature allows you to store different kinds of metadata (e.g., for each migration) without needing a predefined table structure. You can adjust to different fields (e.g., source code, transformation status, issues, etc.) without worrying about rigid relationships.
- Traditional SQL databases (e.g., MySQL, PostgreSQL) are structured around tables, rows, and columns, where each column has a fixed data type. When you’re working with code (which can be complex and highly variable), storing it in a relational schema can be cumbersome. In SQL, you would typically need to define structured fields for every type of data you’re storing (e.g., the code, metadata, status), and this structure may not fit neatly with the flexible nature of code snippets and migration data.

- With MongoDB, you don’t need to create a new table for each change in the schema or store data in rigid columns. This makes MongoDB a better fit for things like storing code snippets or migration metadata, which can have highly variable content.

- MongoDB has several advantages when dealing with data that will be used for machine learning (ML) or large language models (LL).

- Document structure: MongoDB's document format (BSON/JSON) can easily align with the data structures typically used in ML models, such as arrays, objects, and nested data. In contrast, relational databases typically work with structured data that may not map as easily to the types of unstructured or semi-structured data used in ML.

- Scalability: MongoDB is designed for horizontal scaling, which means it can handle large volumes of data and requests. This is important when working with ML models, which can require large datasets and need the ability to scale easily as your project grows.

- Real-time updates: MongoDB supports real-time data updates, which is useful in a migration pipeline where the system continuously interacts with code snippets or when you need to process and update data in real time, a typical need when working with ML/AI systems.

- Data transmission: In general, working with data in JSON-like formats (which MongoDB uses) is often more natural when you're sending data between services or to/from models, as many ML tools and frameworks (such as TensorFlow, PyTorch, or OpenAI's API) work well with JSON or JSON-like structures.
  (thats a broad statement, and not quite sure i can back that up, but most of the information above came from 'designing data intensive applications' - an actual book -wut)

1. **Challenge**: Setting up the development environment consistently across different machines.

   **Solution**: Created detailed environment setup instructions in README.md and will implement Docker containers in a future iteration.

#### Best Practices Identified

1. **API-First Design**: Defining all endpoints and their request/response formats before implementation helps maintain consistency and reduces rework.

2. **Separation of Concerns**: Structuring the API to clearly separate:

   - Authentication/authorization
   - Project management
   - Code transformation
   - Analytics/feedback

3. **Progressive Enhancement**: Planning the roadmap in phases allows for a functioning MVP before adding advanced features.

## Starting Client Implementation

### Day 2: Setting Up Next.js Frontend

#### What Went Well

1. **Creating Next.js Project**: Used create-next-app with TypeScript support:

```bash
npx create-next-app@latest client --typescript --eslint --tailwind --app
cd client
```

2. **Frontend Directory Structure**: Organized the project with scalability in mind:

```
client/
├── app/                  # Next.js App Router
│   ├── (auth)/           # Authentication routes
│   │   ├── login/        # Login page
│   │   └── register/     # Registration page
│   ├── dashboard/        # Dashboard layout
│   │   ├── projects/     # Projects listing
│   │   └── [id]/         # Individual project view
│   ├── layout.tsx        # Root layout component
│   └── page.tsx          # Landing page
├── components/           # Reusable components
│   ├── ui/               # UI primitives
│   ├── forms/            # Form components
│   ├── dashboard/        # Dashboard-specific components
│   └── editor/           # Code editor components
├── lib/                  # Utility functions
│   ├── api.ts            # API client
│   └── auth.ts           # Authentication utilities
└── types/                # TypeScript type definitions
```

#### Challenges & Solutions

1. **Challenge**: Setting up Monaco Editor (VS Code's editor) in Next.js with SSR.

   **Solution**: Used dynamic imports with `next/dynamic` to load the editor component client-side only:

```typescript
import dynamic from "next/dynamic";

const CodeEditor = dynamic(() => import("@/components/editor/CodeEditor"), {
  ssr: false,
});
```

2. **Challenge**: Managing authentication state across the application.

   **Solution**: Implemented React Context for auth state and localStorage for persisting tokens:

```typescript
// In lib/auth.ts
export const AuthContext = createContext<AuthContextType>({
  user: null,
  isLoading: true,
  login: async () => false,
  logout: () => {},
  register: async () => false,
});

// In app/layout.tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        <AuthProvider>{children}</AuthProvider>
      </body>
    </html>
  );
}
```

3. **Challenge**: API client implementation that handles authentication.

   **Solution**: Created a fetch wrapper that automatically adds auth tokens and handles common errors:

```typescript
// In lib/api.ts
export async function apiFetch<T>(
  endpoint: string,
  options: RequestInit = {}
): Promise<T> {
  const token = localStorage.getItem("authToken");

  const headers = {
    "Content-Type": "application/json",
    ...(token ? { Authorization: `Bearer ${token}` } : {}),
    ...options.headers,
  };

  const response = await fetch(
    `${process.env.NEXT_PUBLIC_API_URL}${endpoint}`,
    {
      ...options,
      headers,
    }
  );

  if (!response.ok) {
    // Handle specific error status codes
    if (response.status === 401) {
      // Clear invalid token and redirect to login
      localStorage.removeItem("authToken");
      window.location.href = "/login";
      throw new Error("Authentication required");
    }

    // Try to parse error response
    const errorData = await response.json().catch(() => null);
    throw new Error(errorData?.message || "API request failed");
  }

  return response.json();
}
```

#### Best Practices Identified

1. **Type Safety**: Using TypeScript throughout the project improves code quality and developer experience:

```typescript
// In types/api.ts
export interface Project {
  id: string;
  name: string;
  description?: string;
  sourceFramework: string;
  targetFramework: string;
  status: "pending" | "in_progress" | "completed" | "failed";
  createdAt: string;
  updatedAt: string;
}

// In components/dashboard/ProjectCard.tsx
interface ProjectCardProps {
  project: Project;
  onSelect: (project: Project) => void;
}
```

2. **Component Composition**: Building UI from small, reusable components:

```typescript
// Button component with variants
export function Button({
  variant = "primary",
  size = "medium",
  children,
  ...props
}: ButtonProps) {
  const variantClasses = {
    primary: "bg-blue-600 hover:bg-blue-700 text-white",
    secondary: "bg-gray-200 hover:bg-gray-300 text-gray-800",
    danger: "bg-red-600 hover:bg-red-700 text-white",
  };

  const sizeClasses = {
    small: "py-1 px-2 text-sm",
    medium: "py-2 px-4",
    large: "py-3 px-6 text-lg",
  };

  return (
    <button
      className={`rounded font-medium transition-colors ${variantClasses[variant]} ${sizeClasses[size]}`}
      {...props}
    >
      {children}
    </button>
  );
}
```

3. **Environment Configuration**: Using `.env.local` for development and `.env.production` for production environments.

#### Code Snippets

Here's our implementation of the login form component:

```tsx
// In components/forms/LoginForm.tsx
"use client";

import { useState } from "react";
import { useAuth } from "@/lib/auth";
import { Button } from "@/components/ui/Button";
import { TextInput } from "@/components/ui/TextInput";

export function LoginForm() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [error, setError] = useState<string | null>(null);
  const [isLoading, setIsLoading] = useState(false);
  const { login } = useAuth();

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    setError(null);
    setIsLoading(true);

    try {
      const success = await login(email, password);
      if (!success) {
        setError("Invalid email or password");
      }
    } catch (err) {
      setError((err as Error).message || "Login failed");
    } finally {
      setIsLoading(false);
    }
  };

  return (
    <form onSubmit={handleSubmit} className="space-y-4">
      {error && (
        <div className="p-3 bg-red-100 border border-red-400 text-red-700 rounded">
          {error}
        </div>
      )}

      <div>
        <label
          htmlFor="email"
          className="block text-sm font-medium text-gray-700"
        >
          Email
        </label>
        <TextInput
          id="email"
          type="email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          required
          className="mt-1 w-full"
        />
      </div>

      <div>
        <label
          htmlFor="password"
          className="block text-sm font-medium text-gray-700"
        >
          Password
        </label>
        <TextInput
          id="password"
          type="password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          required
          className="mt-1 w-full"
        />
      </div>

      <Button
        type="submit"
        variant="primary"
        className="w-full"
        disabled={isLoading}
      >
        {isLoading ? "Logging in..." : "Log in"}
      </Button>
    </form>
  );
}
```

## Lessons Learned So Far

1. **Planning Saves Time**: The investment in planning API structure and project architecture upfront is already paying dividends in faster implementation.

2. **TypeScript Integration**: Using TypeScript from the beginning helps catch errors early and provides better IDE support.

3. **Component-Based Development**: Breaking the UI into small, reusable components makes development more manageable and scalable.

4. **Authentication Patterns**: Implementing auth early establishes patterns for protected routes and API calls.

5. **Next.js App Router**: The new App Router in Next.js provides a more intuitive way to handle layouts and nested routes compared to the Pages Router.

---

This document will be continuously updated as development progresses, and individual "HowTo's" will be added for each phase, such that this document doesn't sprawl too much.
