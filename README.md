# Adding Tailwind CSS to ASP.NET 7.0 MVC Project

This guide describes integrating Tailwind CSS into an ASP.NET Core project.

## Prerequisites
- [.NET SDK](https://dotnet.microsoft.com/download) - ^7.0+
- Node.js must be installed on your system. If not, you can download and install it from the [official Node.js website](https://nodejs.org/).

## Steps

### Step 1: Initialize npm

Open a terminal in your ASP.NET Core project directory and run:

```bash
npm init -y
```
This command will create a default package.json file with standard settings.
  
---
### Step 2: Install Tailwind CSS and Dependencies

In the terminal, run:

```bash
npm install tailwindcss postcss autoprefixer
```
This command will install Tailwind CSS, PostCSS, and Autoprefixer, which are necessary for building and customizing Tailwind CSS.

---
### Step 3: Generate Tailwind Config File

Create a `tailwind.config.js` file in the project's root directory:

```bash
npx tailwindcss init
```
This command creates a default Tailwind configuration file which you can customize according to your project's design requirements.

---
### Step 4: Create Your CSS File

Update and replace all content in the site.css found in the `wwwroot/css` directory by adding the following Tailwind directives.
If for some reason you don't have site.css, create it.

```css
@import 'tailwindcss/base';
@import 'tailwindcss/components';
@import 'tailwindcss/utilities';
```

 This file will include all Tailwind CSS classes, which will be processed to generate your final stylesheet.

---
### Step 5: Integrating Tailwind Build into MSBuild Process

To ensure our Tailwind CSS is built and up-to-date before every build of our ASP.NET Core application, we modify the project file to include our `site.css` and `tailwind.config.js` in the list of files to check. Additionally, we define a custom MSBuild target to run our Tailwind build script:

```xml
<ItemGroup>
  <UpToDateCheckBuilt Include="wwwroot/css/site.css" Set="Css" />
  <UpToDateCheckBuilt Include="tailwind.config.js" Set="Css" />
</ItemGroup>

<Target Name="Tailwind" BeforeTargets="Build">
  <Exec Command="npm run css:build"/>
</Target>
```

This setup ensures that any changes made to the Tailwind configuration or the site's CSS trigger a rebuild of the Tailwind output, keeping styles synchronized with your project's development.

---
### Step 6: Process CSS with Tailwind

Add a build script to your `package.json` file to enable processing your CSS file with Tailwind:

```json
"scripts": {
  "build": "tailwindcss build wwwroot/css/site.css -o wwwroot/css/tailwind.css"
}
```
This script uses the Tailwind CLI to build your CSS, applying all Tailwind styles and utilities to the styles.css file and outputting the final CSS to tailwind.css.

---
### Step 7: Build the Tailwind CSS

Run the build script to generate `tailwind.css` which will contain all the processed Tailwind CSS styles:

```bash
npm run build
```
This will create the tailwind.css file in your wwwroot/css directory with all of Tailwind's utility classes ready to be used in your project.

---
### Step 7: Reference the Output CSS

Link the generated Tailwind CSS file in your `_Layout.cshtml` or other shared layout file. Add the following line to the `<head>` section of your HTML:

```html
<link rel="stylesheet" href="~/css/tailwind.css">
```
By adding this link to your layout file, you make the Tailwind styles available throughout all the views that use this layout.

---
### Step 8: (Optional) Setup for Production

To prepare your Tailwind CSS for production, you'll want to remove any unused CSS for a smaller file size. Install the `@fullhuman/postcss-purgecss` package to help with this:

```bash
npm install @fullhuman/postcss-purgecss
```
Then, update the postcss.config.js file to include the PurgeCSS plugin configuration:

```json
module.exports = {
  plugins: [
    require('tailwindcss'),
    require('autoprefixer'),
    require('@fullhuman/postcss-purgecss')({
      content: ['./**/*.cshtml'],
      defaultExtractor: content => content.match(/[\w-/:]+(?<!:)/g) || [])
    })
  ]
};
```
This setup will ensure that the final CSS file includes only the styles that are actually used in your HTML and Razor view files.

---
### Step 9: Use Tailwind in Your Views

Now that Tailwind CSS is integrated into your project, you can use its utility classes in your Razor views. Start by applying Tailwind's classes to your HTML elements to style them according to your design:

```html
<button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
  Click me
</button>
```
You can take advantage of Tailwind's responsive prefixes, state variants, and more to create a fully responsive and interactive user interface.
