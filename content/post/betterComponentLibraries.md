---
title: "Better Component Libraries"
date: 2024-09-10
---

## What are Component Libraries?

In front-end development, a component library is a collection of reusable components that can be used to build a website or web application (Buttons, switches, navbar, dropdowns, table, ect.). These components are typically designed to be modular, meaning they can be easily combined and customized to create a wide variety of layouts and designs.

Often, times component libraries are imported into a project as a package, like

```javascript
import { Button, Card, Modal } from "my-component-library";
```

But this approach can lead to an increase in unessary abstractions and dependencies. A better approach is to use a component library that is built with a framework like React or Vue, and then import the components directly into your project. Like Shadcn

## Shadcn

Shadcn [https://ui.shadcn.com/] is NOT a component library, but a collection of components that can be used to build a website or web application. Shadcn is not distributed from NPM or any other package manger, but instead is a open source collection of componets that we can customize to our needs. The approach that Shadcn recommends we take is to copy and paste the components directly into your project, or even better... make our own componet libary.

Here are some of the benefits of using Shadcn:

- Headless UI
- Accessible by default

# Headless UI

UI components that are not styled, but are fully functional are called headless UI components. This is how Shadcn is able to seperate the implementation from the styling. This allows us to use the components in any project, and style them toto fit the project's needs.

# Fully Functional and Accessible by default

If you have ever tried to create any custom components, you know how difficult it can be to make them functional let alone accessible. Shadcn has not reinvented the wheel with their components, but instead is leveraging Radix to ensure that the components are accessible by default. Here is a simple example of the different accesisbility features that we would have to consider when creating a simple button component-

- Full keyboard navigation
- Supports assistive technology
- Collision and origin-aware animations

Our button component would have to be able to handle all of these features, and more. Shadcn has already done the work for us, and we can leverage their components to ensure that our project is not only functional but accessible.

# Conclusion

Shadcn is a great resource for creating a custom component library, without the need for opiniated third partys abstracting your styles and implementation.
