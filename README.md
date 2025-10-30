# NextJS

## Form Submission in Next.js

There are two primary ways to handle form submissions with Server Actions in Next.js.

### 1. Using Server Actions with Server Components

This is the most direct approach. The form is rendered in a Server Component, and the `action` prop is bound directly to a Server Action. This method leverages progressive enhancement.

#### `src/lib/action.ts`

The server action is defined in a file with the `"use server";` directive. It accepts `FormData`.

```typescript
"use server";

export const registerUser = async (formData: FormData) => {
  const { name, email, password } = Object.fromEntries(formData.entries());

  console.log("Data from Server Component form:", { name, email, password });
  // Add your user registration logic here (e.g., database insertion)
};
```

server component / or client component

src/app/register/register-form.tsx

```tsx
import { registerUser } from "@/lib/action";

const SignupForm: React.FC = async () => {
  return (
    <form
      action={registerUser}
      className="flex flex-col space-y-4"
      onSubmit={handleSubmit}
    >
      <h1 className="text-center">Sign Up</h1>
      <input
        type="text"
        name="name"
        placeholder="Name"
        value={formData.name}
        onChange={handleChange}
      />
      <input
        type="email"
        name="email"
        placeholder="Email"
        value={formData.email}
        onChange={handleChange}
      />
      <input
        type="password"
        name="password"
        placeholder="Password"
        value={formData.password}
        onChange={handleChange}
      />
      <button type="submit">Sign Up</button>
    </form>
  );
};

export default SignupForm;
```

### 2. React old way with server action

src/lib/action.ts

```ts
"use server";

export const registerUser = async (formData: {
  name: string;
  email: string;
  password: string;
}) => {
  const { name, email, password } = formData;

  console.log(name, email, password);
};
```

src/app/register/register-form.tsx

```tsx
"use client";

import { registerUser } from "@/lib/action";
import React, { useState } from "react";

interface SignupFormData {
  name: string;
  email: string;
  password: string;
}

const SignupForm: React.FC = () => {
  const [formData, setFormData] = useState<SignupFormData>({
    name: "",
    email: "",
    password: "",
  });

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setFormData({
      ...formData,
      [e.target.name]: e.target.value,
    });
  };

  const handleSubmit = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();

    const registerFormData = {
      name: formData.name,
      email: formData.email,
      password: formData.password,
    };

    await registerUser(registerFormData);
  };

  return (
    <form
      action={""}
      className="flex flex-col space-y-4"
      onSubmit={handleSubmit}
    >
      <h1 className="text-center">Sign Up</h1>
      <input
        type="text"
        name="name"
        placeholder="Name"
        value={formData.name}
        onChange={handleChange}
      />
      <input
        type="email"
        name="email"
        placeholder="Email"
        value={formData.email}
        onChange={handleChange}
      />
      <input
        type="password"
        name="password"
        placeholder="Password"
        value={formData.password}
        onChange={handleChange}
      />
      <button type="submit">Sign Up</button>
    </form>
  );
};

export default SignupForm;
```
