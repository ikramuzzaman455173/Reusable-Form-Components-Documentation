### Complete Documentation for Reusable Form Input with React Hook Form, Tailwind CSS, Zod, and Yup Validation

## **Table of Contents**

1. [Introduction](#1-introduction)
2. [Setup and Installation](#2-setup-and-installation)
3. [FormInput Component Overview](#3-forminput-component-overview)
4. [Supporting Different Input Types](#4-supporting-different-input-types)
5. [Custom Styling for Each Input Field](#5-custom-styling-for-each-input-field)
6. [Validation with React Hook Form](#6-validation-with-react-hook-form)
    - [Custom Validation](#6-1-custom-validation)
    - [Validation with Zod](#6-2-validation-with-zod)
    - [Validation with Yup](#6-3-validation-with-yup)
7. [Error Handling](#7-error-handling)
8. [How to Use the Reusable FormInput Component](#8-how-to-use-the-reusable-forminput-component)
9. [Conclusion](#9-conclusion)

---

## **1. Introduction**

This documentation details how to build a **reusable form input component** that integrates with **React Hook Form** (for form handling) and supports **Tailwind CSS** for custom styling. It also covers how to use **Zod** and **Yup** for validation in addition to custom validation handling.

---

## **2. Setup and Installation**

### Install dependencies

To begin, install the necessary packages:

```bash
npm install react-hook-form zod yup tailwindcss
```

Make sure Tailwind CSS is set up in your project by following the [official installation guide](https://tailwindcss.com/docs/installation).

---

## **3. FormInput Component Overview**

This `FormInput` component will handle different input types (e.g., text, email, password) and provide validation via **React Hook Form**, along with support for **custom validation**, **Zod**, and **Yup**.

### FormInput Component Code:

```javascript
// components/FormInput.js
import React from 'react';
import { ErrorMessage } from '@hookform/error-message';

const FormInput = ({
  type = 'text',
  name,
  label,
  options = [], // For select or radio buttons
  register,
  rules,
  errors,
  placeholder,
  multiple = false,
  className = '', // Additional custom className for styling
}) => {
  return (
    <div className="mb-4">
      {label && <label className="block mb-2 text-gray-700">{label}</label>}

      {/* Text, Email, Password, Number, Date */}
      {['text', 'email', 'password', 'number', 'tel', 'date'].includes(type) && (
        <input
          type={type}
          {...register(name, rules)}
          placeholder={placeholder}
          className={`w-full p-2 border rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 
                      ${errors[name] ? 'border-red-500' : 'border-gray-300'} ${className}`}
        />
      )}

      {/* File Input */}
      {type === 'file' && (
        <input
          type="file"
          {...register(name, rules)}
          className={`w-full p-2 border rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 
                      ${errors[name] ? 'border-red-500' : 'border-gray-300'} ${className}`}
        />
      )}

      {/* Select Input */}
      {type === 'select' && (
        <select
          {...register(name, rules)}
          className={`w-full p-2 border rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 
                      ${errors[name] ? 'border-red-500' : 'border-gray-300'} ${className}`}
          multiple={multiple}
        >
          <option value="" disabled>{placeholder}</option>
          {options.map((option, index) => (
            <option key={index} value={option.value}>
              {option.label}
            </option>
          ))}
        </select>
      )}

      {/* Radio Input */}
      {type === 'radio' && (
        <div className="space-y-2">
          {options.map((option, index) => (
            <label key={index} className="flex items-center space-x-2">
              <input
                type="radio"
                value={option.value}
                {...register(name, rules)}
                className="form-radio text-blue-600"
              />
              <span>{option.label}</span>
            </label>
          ))}
        </div>
      )}

      {/* Checkbox Input */}
      {type === 'checkbox' && (
        <label className="flex items-center space-x-2">
          <input
            type="checkbox"
            {...register(name, rules)}
            className="form-checkbox text-blue-600"
          />
          <span>{label}</span>
        </label>
      )}

      {/* Error message */}
      <ErrorMessage
        errors={errors}
        name={name}
        render={({ message }) => <p className="text-red-500 text-sm mt-1">{message}</p>}
      />
    </div>
  );
};

export default FormInput;
```

This component supports various input types, including text, email, select, radio, and checkbox fields. It allows you to pass custom styling via the `className` prop and integrates with **React Hook Form** for form state and validation.

---

## **4. Supporting Different Input Types**

The `FormInput` component can handle the following input types:

- **Text, Email, Password, Number, Tel, Date**: Standard form input fields.
- **Select**: Dropdown menu.
- **Radio**: Multiple radio buttons.
- **Checkbox**: A checkbox input.
- **File**: A file input.

---

## **5. Custom Styling for Each Input Field**

You can pass **custom Tailwind CSS classes** for specific styling of each input field using the `className` prop.

### Example of Custom Styling:

```javascript
<FormInput
  type="text"
  name="username"
  label="Username"
  register={register}
  rules={{ required: 'Username is required' }}
  errors={errors}
  className="border-blue-500 focus:ring-4 focus:ring-blue-500"
/>
```

---

## **6. Validation with React Hook Form**

### **6.1 Custom Validation**

Custom validation rules can be defined directly inside the `rules` prop when using **React Hook Form**.

### Example of Custom Validation:

```javascript
<FormInput
  type="text"
  name="username"
  label="Username"
  register={register}
  rules={{
    required: 'Username is required',
    validate: (value) => value.length > 3 || 'Username must be longer than 3 characters',
  }}
  errors={errors}
/>
```

Here, we added custom validation for the `username` field, ensuring that the username is required and must be longer than 3 characters.

---

### **6.2 Validation with Zod**

**Zod** is a schema validation library that integrates seamlessly with **React Hook Form**. You can define your validation schema with Zod and apply it using the `zodResolver`.

#### Example: Using Zod Validation:

```javascript
import { useForm } from 'react-hook-form';
import { z } from 'zod';
import { zodResolver } from '@hookform/resolvers/zod';
import FormInput from './FormInput';

// Define Zod schema
const schema = z.object({
  username: z.string().min(1, 'Username is required'),
  email: z.string().email('Invalid email address'),
  password: z.string().min(8, 'Password must be at least 8 characters'),
});

const MyForm = () => {
  const { register, handleSubmit, formState: { errors } } = useForm({
    resolver: zodResolver(schema),
  });

  const onSubmit = (data) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <FormInput
        type="text"
        name="username"
        label="Username"
        register={register}
        errors={errors}
      />
      <FormInput
        type="email"
        name="email"
        label="Email"
        register={register}
        errors={errors}
      />
      <FormInput
        type="password"
        name="password"
        label="Password"
        register={register}
        errors={errors}
      />
      <button type="submit">Submit</button>
    </form>
  );
};
```

### Key Points:
- The `zodResolver` integrates the **Zod** schema with **React Hook Form**.
- Zod schemas allow defining strict validation rules.

---

### **6.3 Validation with Yup**

**Yup** is a popular schema validation library that is widely used with **React Hook Form**. Below is how to use **Yup** for validation.

#### Example: Using Yup Validation:

```javascript
import { useForm } from 'react-hook-form';
import * as Yup from 'yup';
import { yupResolver } from '@hookform/resolvers/yup';
import FormInput from './FormInput';

// Define Yup schema
const validationSchema = Yup.object({
  username: Yup.string().required('Username is required'),
  email: Yup.string().email('Invalid email address').required('Email is required'),


  password: Yup.string().min(8, 'Password must be at least 8 characters').required('Password is required'),
});

const MyForm = () => {
  const { register, handleSubmit, formState: { errors } } = useForm({
    resolver: yupResolver(validationSchema),
  });

  const onSubmit = (data) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <FormInput
        type="text"
        name="username"
        label="Username"
        register={register}
        errors={errors}
      />
      <FormInput
        type="email"
        name="email"
        label="Email"
        register={register}
        errors={errors}
      />
      <FormInput
        type="password"
        name="password"
        label="Password"
        register={register}
        errors={errors}
      />
      <button type="submit">Submit</button>
    </form>
  );
};
```

### Key Points:
- The `yupResolver` integrates **Yup** with **React Hook Form**.
- Yup schemas allow flexible and readable validation definitions.

---

## **7. Error Handling**

Error messages are displayed under each input field when validation fails. This is handled using the `ErrorMessage` component from **React Hook Form**.

### Error Message Example:

```javascript
<ErrorMessage
  errors={errors}
  name="username"
  render={({ message }) => <p className="text-red-500 text-sm">{message}</p>}
/>
```

---

## **8. How to Use the Reusable FormInput Component**

Now that the `FormInput` component is built, you can use it inside any form as follows:

### Example Usage:

```javascript
import { useForm } from 'react-hook-form';
import FormInput from './FormInput';

const MyForm = () => {
  const { register, handleSubmit, formState: { errors } } = useForm();

  const onSubmit = (data) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <FormInput
        type="text"
        name="username"
        label="Username"
        register={register}
        rules={{ required: 'Username is required' }}
        errors={errors}
        className="border-blue-500 focus:ring-4 focus:ring-blue-500"
      />
      <FormInput
        type="email"
        name="email"
        label="Email"
        register={register}
        rules={{ required: 'Email is required' }}
        errors={errors}
        className="border-gray-300 focus:ring-4 focus:ring-green-500"
      />
      <button type="submit">Submit</button>
    </form>
  );
};
```

---

## **9. Conclusion**

This solution allows you to create **flexible, reusable form input components** with **custom validation**, **Zod**, and **Yup** for validation, styled with **Tailwind CSS**. It integrates seamlessly with **React Hook Form** and enables quick form creation and validation. By using this approach, you reduce code duplication, make the forms easier to manage, and ensure a smooth user experience.
