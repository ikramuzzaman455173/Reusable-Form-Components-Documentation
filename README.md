## **Reusable Form Components Documentation**

### **Overview**

The goal of this implementation is to create reusable, flexible form components that handle various form field types (text inputs, select, checkboxes, radios, file uploads, etc.) using **React Hook Form** and **Yup** for validation. The forms are styled using **Tailwind CSS** for a responsive and modern design.

---

### **Key Concepts**

- **React Hook Form (RHF)**: A library for managing form state, validation, and submission in React.
- **Yup**: A validation library used for defining schema validation.
- **Tailwind CSS**: A utility-first CSS framework used for styling the components.

---

### **1. Setting Up React Hook Form**

To start using **React Hook Form** and **Yup**, you'll need to install them in your project:

```bash
npm install react-hook-form yup @hookform/resolvers
```

### **2. Creating the `FormInput` Component**

This is a reusable component that supports various input types. It handles form input fields such as `text`, `email`, `password`, `select`, `checkbox`, `radio`, and `file` uploads.

#### **FormInput.js**

```jsx
// components/FormInput.js
import React from 'react';
import { ErrorMessage } from '@hookform/error-message';

const FormInput = ({
  type = 'text', // Default type is text
  name,
  label,
  options = [], // Options for select, radio buttons
  register,
  rules,
  errors,
  placeholder,
  multiple = false, // For multi-select
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
          className={`w-full p-2 border ${errors[name] ? 'border-red-500' : 'border-gray-300'} rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500`}
        />
      )}

      {/* File Input */}
      {type === 'file' && (
        <input
          type="file"
          {...register(name, rules)}
          className={`w-full p-2 border ${errors[name] ? 'border-red-500' : 'border-gray-300'} rounded-md`}
        />
      )}

      {/* Select Input (Multi-select or Single-select) */}
      {type === 'select' && (
        <select
          {...register(name, rules)}
          className={`w-full p-2 border ${errors[name] ? 'border-red-500' : 'border-gray-300'} rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500`}
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

#### **Props for FormInput Component:**

- **type**: The type of input (e.g., `text`, `email`, `password`, `select`, `radio`, `checkbox`, `file`). Defaults to `text`.
- **name**: The name attribute for the form field, used in `React Hook Form`.
- **label**: The label text for the input field.
- **options**: Array of options for select or radio inputs (e.g., `[ { label: 'Option 1', value: 'option1' } ]`).
- **register**: The `register` function from `useForm()` of React Hook Form.
- **rules**: Validation rules defined via `Yup`.
- **errors**: Validation errors from React Hook Form.
- **placeholder**: Placeholder text for input fields.
- **multiple**: Boolean for supporting multi-select in select inputs.

---

### **3. Creating the Reusable Form Component**

This component will handle the form submission, validation, and the rendering of `FormInput` components dynamically based on a fields configuration.

#### **ReusableForm.js**

```jsx
// components/ReusableForm.js
import React from 'react';
import { useForm, Controller } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';
import FormInput from './FormInput';

const ReusableForm = ({ fields, validationSchema, onSubmit }) => {
  const { register, handleSubmit, formState: { errors } } = useForm({
    resolver: yupResolver(validationSchema),
  });

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="w-full max-w-md mx-auto bg-white p-6 shadow-md rounded-lg">
      {fields.map((field, index) => (
        <FormInput
          key={index}
          name={field.name}
          label={field.label}
          type={field.type}
          options={field.options || []}
          register={register}
          rules={field.rules || {}}
          errors={errors}
          placeholder={field.placeholder}
          multiple={field.multiple || false}
        />
      ))}
      
      <div className="mt-6">
        <button
          type="submit"
          className="w-full py-2 px-4 bg-blue-600 text-white rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 hover:bg-blue-700"
        >
          Submit
        </button>
      </div>
    </form>
  );
};

export default ReusableForm;
```

#### **Props for ReusableForm Component:**

- **fields**: An array of field objects that define the input fields to be rendered in the form.
- **validationSchema**: The **Yup** schema used for validating the form.
- **onSubmit**: A function that handles the form submission.

---

### **4. Example Usage:**

#### **Registration Form Example**

```jsx
// pages/register.js
import React from 'react';
import * as Yup from 'yup';
import ReusableForm from '../components/ReusableForm';

const Register = () => {
  const fields = [
    { name: 'name', label: 'Name', type: 'text', placeholder: 'Enter your name', rules: { required: 'Name is required' } },
    { name: 'email', label: 'Email', type: 'email', placeholder: 'Enter your email', rules: { required: 'Email is required' } },
    { name: 'password', label: 'Password', type: 'password', placeholder: 'Enter your password', rules: { required: 'Password is required' } },
    { name: 'gender', label: 'Gender', type: 'radio', options: [{ label: 'Male', value: 'male' }, { label: 'Female', value: 'female' }], rules: { required: 'Gender is required' } },
    { name: 'profileImage', label: 'Profile Image', type: 'file', rules: { required: 'Profile image is required' } },
  ];

  const validationSchema = Yup.object({
    name: Yup.string().required('Name is required'),
    email: Yup.string().email('Invalid email').required('Email is required'),
    password: Yup.string().min(6, 'Password must be at least 6 characters').required('Password is required'),
    gender: Yup.string().required('Gender is required'),
    profileImage: Yup.mixed().required('A profile image is required').test('fileSize', 'File is too large', (value) => value && value[0].size <= 5000000),
  });

  const handleRegisterSubmit = (data) => {
    console.log('Register Form Submitted:', data);
  };

  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-100">
      <ReusableForm fields={fields} validationSchema={validationSchema} onSubmit={handleRegisterSubmit} />
    </div>
  );
};

export default Register;
```

### **5. Error Handling & Styling**

- **Error Handling**: Each field component is capable of displaying error messages

 if validation fails. The errors are passed to the `FormInput` component and displayed conditionally.
- **Tailwind CSS Styling**: You can customize the input, label, and button styles using **Tailwind** classes. The classes used ensure responsiveness and modern design.

### **Conclusion**

This approach enables the easy creation of flexible, reusable form components that can handle various types of form inputs (text, password, select, radio, file upload, etc.). React Hook Form and Yup make it simple to manage form state and validation, while Tailwind CSS ensures your form is styled in a responsive and modern way.

