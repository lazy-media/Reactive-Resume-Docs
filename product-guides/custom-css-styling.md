---
icon: file-code
---

# Custom CSS Styling

_<mark style="color:red;">**Please be aware, that this is a rough example. This particular page was made using AI (GitHub CoPilot). This information may not be entirely accurate.**</mark>_

Reactive Resume allows you to personalize your resume even further through custom CSS. This feature lets you override or extend the default styles, providing full control over the look and feel of your final document. This guide explains how to use, write, and apply custom CSS within the application.

***

### Table of Contents

* [What Is Custom CSS in Reactive Resume?](custom-css-styling.md#what-is-custom-css-in-reactive-resume)
* [Where to Find the Custom CSS Editor](custom-css-styling.md#where-to-find-the-custom-css-editor)
* [How to Enable and Apply Custom CSS](custom-css-styling.md#how-to-enable-and-apply-custom-css)
* [Writing Your Custom CSS](custom-css-styling.md#writing-your-custom-css)
  * [Using CSS Variables](custom-css-styling.md#using-css-variables)
  * [Targeting Elements](custom-css-styling.md#targeting-elements)
  * [Example: Changing Section Titles](custom-css-styling.md#example-changing-section-titles)
  * [Example: Dark Mode Adjustments](custom-css-styling.md#example-dark-mode-adjustments)
* [Tips for Writing Production-Ready CSS](custom-css-styling.md#tips-for-writing-production-ready-css)
* [Print Optimization](custom-css-styling.md#print-optimization)
* [Reference: Available CSS Variables](custom-css-styling.md#reference-available-css-variables)
* [Troubleshooting](custom-css-styling.md#troubleshooting)
* [Sample Production-Ready Custom CSS](custom-css-styling.md#sample-production-ready-custom-css)

***

### What Is Custom CSS in Reactive Resume?

Custom CSS in Reactive Resume gives users the ability to inject their own CSS rules into the resume preview and exported documents. This empowers you to:

* Match your personal or brand style
* Fix layout issues
* Add print-specific optimizations
* Fully control the visual hierarchy

Custom CSS is applied in addition to the default styles and can override them as needed.

***

### Where to Find the Custom CSS Editor

1. **Open your resume** in Reactive Resume.
2. Navigate to the **Builder** interface.
3. On the **right sidebar**, find the section labeled **Custom CSS**.
4. You will see:
   * A toggle labeled **Apply Custom CSS**
   * A code editor where you can write your CSS

***

### How to Enable and Apply Custom CSS

1. **Toggle on** the “Apply Custom CSS” switch.
2. **Write or paste** your CSS into the editor provided.
3. The changes will **preview live** on your resume.
4. When you export or share your resume, your custom CSS will be included in the rendered output.

***

### Writing Your Custom CSS

Reactive Resume uses [Tailwind CSS](https://tailwindcss.com/) as well as its own design tokens and variables for theming. For best results:

* Use the provided CSS variables (see Reference)
* Inspect elements in your resume preview using browser dev tools to find class names or structures you want to override
* Write CSS selectors that target only the elements you wish to change

#### Using CSS Variables

Reactive Resume exposes a set of theme-aware CSS variables for colors, backgrounds, borders, etc.\
**Example:**

```css
body {
  background: var(--background);
  color: var(--foreground);
}
```

#### Targeting Elements

Targeting elements by class or HTML tag works as expected.\
**Example:**

```css
.section-title {
  color: var(--primary-accent);
  border-left: 4px solid var(--primary);
  padding-left: 1rem;
}
```

#### Example: Changing Section Titles

```css
.section-title {
  font-size: 1.5rem;
  font-weight: 600;
  color: var(--primary);
  border-bottom: 2px solid var(--primary-accent);
}
```

#### Example: Dark Mode Adjustments

You can target dark mode specifically using the `.dark` class (automatically set by Reactive Resume):

```css
.dark body {
  background: var(--background);
  color: var(--foreground);
}

.dark .section-title {
  color: var(--primary-accent);
}
```

***

### Tips for Writing Production-Ready CSS

* **Leverage CSS variables**: This ensures your styling respects light/dark theme changes.
* **Be specific with selectors**: Use class names, not generic tags, to avoid unintended overrides.
* **Avoid `!important` unless necessary**: Only use it to override highly-specific defaults.
* **Test for print and screen**: Use `@media print` for print-specific rules.
* **Keep CSS concise and organized**: Comment your code for clarity.

***

### Print Optimization

To optimize your resume for printing, you can use a print media query:

```css
@media print {
  body {
    background: white;
    color: black;
  }
  a {
    color: black;
    text-decoration: underline;
  }
  .section-title {
    color: #222;
    border-left: 3px solid #222;
  }
}
```

***

### Reference: Available CSS Variables

Reactive Resume defines the following variables (auto-switched for light/dark mode):

```css
--background              /* Main background */
--foreground              /* Main text color */
--border                  /* Border color */

--primary                 /* Primary accent */
--primary-accent          /* Primary accent variant */
--primary-foreground      /* Text on primary background */

--secondary               /* Secondary background */
--secondary-accent        /* Secondary accent */
--secondary-foreground    /* Text on secondary background */

--error                   /* Error state */
--error-accent
--error-foreground

--warning                 /* Warning state */
--warning-accent
--warning-foreground

--info                    /* Info state */
--info-accent
--info-foreground

--success                 /* Success state */
--success-accent
--success-foreground
```

You can also reference Tailwind utility classes and inspect the DOM for more fine-grained targeting.

***

### Troubleshooting

* **My styles don’t apply:**
  * Double-check selector specificity.
  * Use browser dev tools to inspect element classes and structure.
  * Try adding `!important` as a last resort for overrides.
* **Theme variables not working:**
  * Ensure you are using the correct variable names (see above).
  * Use `.dark` selectors for dark mode overrides.
* **Exported PDF/Print looks different:**
  * Use `@media print` to adjust for print layouts.
  * Avoid absolute positioning/fixed elements unless necessary.

***

### Sample Production-Ready Custom CSS

```css
body {
  font-family: 'IBM Plex Sans', Arial, sans-serif;
  background: var(--background);
  color: var(--foreground);
}

.resume-header {
  text-align: center;
  margin-bottom: 2rem;
  border-bottom: 2px solid var(--primary);
}

.resume-header .name {
  font-size: 2.5rem;
  font-weight: 700;
  color: var(--primary);
  letter-spacing: 2px;
}

.section-title {
  font-size: 1.3rem;
  font-weight: 600;
  color: var(--primary-accent);
  border-left: 4px solid var(--primary);
  padding-left: 1rem;
  margin-top: 2rem;
  margin-bottom: 1rem;
}

a {
  color: var(--info);
  text-decoration: underline;
}
a:hover {
  color: var(--info-accent);
}

@media print {
  body {
    background: white;
    color: black;
  }
  .resume-header {
    border-bottom: 1px solid #333;
  }
  .section-title {
    color: #222;
    border-left: 3px solid #222;
  }
}
```

***

**Need more help?**

* Explore the default styles in the repository under `apps/client/src/styles`
* Inspect elements in your resume preview with browser dev tools
