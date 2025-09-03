### Interview Q&A for Drupal Frontend Lead.

1. When would you decide to build a custom theme instead of using a contributed one? What’s the benefit?
Ans. My decision to build a custom theme hinges on a few key factors, but it primarily comes down to the uniqueness of the design and the project's long-term goals.

When I Choose a Custom Theme
I opt for a custom theme when the project meets one or more of these criteria:

Highly Bespoke Design: When the provided designs are completely unique and don't align with the structure of common frameworks like Bootstrap or Foundation. Forcing a unique design into a pre-built theme often leads to "override hell," where you spend more time undoing styles than writing new ones.

Performance is Paramount: Contributed themes, while convenient, can be bloated with CSS and JavaScript for components the project will never use. A custom theme ensures we only load the assets we absolutely need, leading to a faster, more optimized site.

A Component-Based Approach (Design System): If the project uses a design system or a component-based methodology (e.g., Atomic Design), a custom theme is the only practical choice. It allows us to build a library of reusable Twig components that precisely match the design spec and business logic.

Long-Term Maintainability: For large, long-term projects, a custom theme is far more maintainable. The codebase is lean, self-documented, and tailored to the project's specific needs, making it easier for new developers to onboard and for the team to extend functionality in the future.

The Key Benefits
The benefits directly address the reasons for choosing this path:

Clean, Semantic Markup: We control the HTML structure from the ground up, ensuring it’s semantic, accessible, and perfectly suited to the content. We aren't stuck with the markup decisions made by a contrib theme author.

Optimized Performance: By building our own asset pipeline, we have full control over CSS/JS aggregation, minification, and conditional loading. This results in faster page loads and better Lighthouse scores.

Superior Developer Experience (DX): A clean, intentional codebase is simply easier and more enjoyable to work with. This improves team velocity and reduces the chance of bugs.

In short, while a contrib theme is great for rapid prototyping or simple sites, a custom theme is an investment in quality, performance, and maintainability for any serious, long-term project.

2. How do you start the theme setup technically?
Ans. The setup process can be split into two main parts:
-- Creating the basic files so Drupal can recognize the theme.
-- Setting up the modern frontend tooling (for compiling CSS/JS, etc.).

I start by creating the theme under /themes/custom/ with a clear machine name. In the .info.yml file, I define the name, Drupal core version, regions, and base theme — usually Classy if I want sensible markup and stable templates, or Stable if I want full control over HTML output. I also define any libraries the theme will use. Then I register JS and CSS in .libraries.yml, usually organizing them by purpose or component.

I create a templates/ directory to override Twig files and use the page.html.twig or node.html.twig as needed to control layout and classes. Once the theme is enabled, I clear cache and begin theming in coordination with designers or site builders.

3. How do you structure your CSS and JavaScript in a Drupal theme to keep it modular and maintainable?
Ans. My approach to structuring CSS and JavaScript is all about predictability and maintainability. The goal is to create a system where any developer on the team can find what they need, add a new feature without breaking existing ones, and understand the code's purpose at a glance.

For CSS: Component-Based with BEM
Methodology: I standardize on BEM (Block, Element, Modifier). Its strict naming convention (.block__element--modifier) is incredibly effective at preventing style collisions and making the CSS self-documenting. In a Drupal context, a "Block" maps perfectly to a Twig component, like a card, a header, or a search form.

File Structure: I use a component-based folder structure with Sass partials. All the styles for a single component live in their own file. For example:

scss/components/_card.scss

scss/components/_hero-banner.scss

scss/layout/_header.scss
A main style.scss file then imports these partials in a logical order, often following an ITCSS or SMACSS-like structure (settings, tools, base, layout, components, etc.). This makes the codebase modular and easy to navigate.

For JavaScript: Modular and Drupal-Aware
Modularity via Libraries: The foundation is Drupal's Libraries API (mytheme.libraries.yml). I never just dump all JS into a single global file. Instead, I define small, purposeful libraries and attach them only to the specific Twig templates or components that need them. This is critical for performance—we only load the code the user actually needs for that page.

Modern JavaScript (ES6+): I write clean, modern ES6+ JavaScript, organized into modules. I use a build tool like Vite or Webpack to compile and transpile these modules into browser-compatible code. This allows us to use modern features and maintain a clean codebase.

Drupal Behaviors: This is the most important part for Drupal integration. All custom JavaScript that interacts with the DOM is wrapped in a Drupal.behavior. This ensures that our scripts run not only on the initial page load but also re-attach correctly after any AJAX activity, like a Views filter or an infinite scroll. It's the key to making JS reliable in a dynamic Drupal environment.

By combining these strategies, we create a frontend codebase that is organized, performant, and easy for the entire team to manage over the long term.

4. Can you explain the relationship between Twig templates and preprocess functions in Drupal? When would you use one over the other?
Ans. Understanding the division of labor between preprocess functions and Twig templates is what separates a good Drupal themer from a great one. It's all about the separation of concerns.

Let's use an analogy to make it clear. Think of building a webpage like making a pizza 🍕:

-- Preprocess Functions (PHP) are the kitchen, where the chef works.
-- Twig Templates are the pizza counter, where the final assembly happens.

The relationship between Twig templates and preprocess functions is the most important principle in Drupal theming: separation of concerns. Think of it like a professional kitchen.

Preprocess Functions (PHP) are the Kitchen. This is where all the complex preparation and logic happens. The chef gathers raw ingredients (data from Drupal), chops, combines, and cooks them into a finished dish (a structured variable).

Twig Templates are the Dining Room. This is where the beautifully prepared dish is presented to the customer. The focus is purely on presentation—arranging things on the plate, adding a garnish. You would never ask the waiter to start cooking a steak in the dining room.

The Roles
Preprocess functions (*.theme or *.module file) are for preparing variables. Their job is to handle complex logic, manipulate data, and hand off a clean, simple set of variables to the template.

Twig templates (*.html.twig file) are for displaying variables. Their job is to handle presentation logic—looping through items, printing variables, and adding HTML markup. They should be kept as clean and simple as possible.

When to Use One Over the Other
Use a Preprocess Function when you need to:
Perform Complex Logic: If you need to combine multiple fields, check user permissions, or run calculations to create a new value, do it in preprocess.

Example: Creating a single $read_time variable based on the word count of a body field.

Reformat Data: When you need to change data from one format to another.

Example: Turning a raw timestamp into a human-readable "5 minutes ago" string.

Fetch More Data: If your template needs information that isn't already available, the preprocess function is the place to load that extra data.

Example: Loading the full author object to get their profile picture, when only the author ID is available.

Use a Twig Template when you need to:
Loop through an array: Use a for loop to iterate over a list of items prepared by the preprocess function.

Example: {% for item in items %}

Simple Conditional Display: Use an if statement to decide whether to render a piece of markup.

Example: {% if show_image %}`<img src="{{ image_url }}">`{% endif %}

Print Variables and Apply Filters: This is Twig's primary job. Print the clean variables passed from preprocess, using simple filters for display purposes.

Example: {{ title|t }} for translation or {{ event_date|date('M d, Y') }} for formatting.

The golden rule is: If it's complex logic, do it in PHP. If it's simple presentation, do it in Twig. This makes your theme easier to maintain, debug, and for designers or other developers to work with.

5. As a Frontend Lead, how do you ensure the themes you build are accessible and follow best practices for performance and cross-browser compatibility?
Ans. As a lead, my approach is to build these quality gates directly into our team's workflow, making them a proactive habit rather than a reactive task. It breaks down into three key areas:

a. Accessibility (A11y)
Accessibility isn't a feature we add at the end; it's a fundamental requirement from the start.

Foundation in Semantic HTML: We enforce a strict policy of using semantic HTML. Using `<nav>, <main>, <button>`, and proper heading structures solves a huge percentage of accessibility issues before they even start.

Keyboard Navigation is Non-Negotiable: During development and code reviews, one of the first manual checks is to tab through the page. Every interactive element must be reachable and have a clear, visible :focus state.

Automated Tooling: I integrate tools like axe DevTools into our browsers for on-the-spot checks during development. We also add automated accessibility checks using Lighthouse or Pa11y into our CI/CD pipeline to catch regressions.

Screen Reader Testing: For complex components, we do periodic manual testing with a screen reader like NVDA or VoiceOver to ensure the user experience is coherent.

b. Performance Optimization
Performance is a core part of the user experience and a key responsibility of the frontend team.

Aggressive Asset Optimization:

Images: We use modern formats like WebP, implement responsive images with the <picture> tag, and use loading="lazy" for all below-the-fold images.

CSS/JS: Our build process automatically minifies assets. More importantly, we leverage Drupal's Libraries API to ensure we only load the code needed for the components on that specific page.

Minimizing Render-Blocking Resources: We audit our scripts to ensure they use async or defer attributes where appropriate, so the page can render without waiting for non-essential JavaScript to load.

Measurement and Monitoring: We use Google Lighthouse to establish a performance baseline and monitor it with every major deployment. For deep dives, we use WebPageTest.org to identify specific bottlenecks.

c. Cross-Browser Compatibility
Our goal is a consistent and functional experience across all supported browsers.

Define a Support Matrix: At the project kickoff, we define a clear list of supported browsers and versions with the client.

Modern, Progressive CSS: We write modern CSS and use a tool like Autoprefixer in our build process to automatically handle vendor prefixes. This covers most common compatibility issues.

Targeted Testing: For critical user journeys, we use a service like BrowserStack to test on real devices and browsers that we don't have physically, ensuring our implementation works everywhere it's supposed to. We reference caniuse.com constantly to make informed development decisions.

By integrating these practices into our daily workflow—from the initial component build to the final code review—we ensure that every theme we ship is robust, inclusive, and fast.

6. What tools, processes, or standards do you put in place to keep accessibility, performance, and cross-browser testing on the radar for your team? How do you balance those priorities with project deadlines?
Ans. My role as a lead is to make quality a shared, systematic responsibility. It’s not about me being the sole gatekeeper, but about building a process where the whole team is empowered and expected to maintain high standards.

Part 1: Tools, Processes, and Standards
I embed quality checks into every stage of our workflow:

At the Ticket Level: Our "Definition of Done" for any frontend ticket includes passing our quality gates. This means a task isn't considered complete until it meets our baseline for accessibility, performance, and compatibility.

During Development: We use automated tools right in the code editor. Linters for CSS and JS catch syntax errors, and an accessibility linter (like axe DevTools) allows developers to run a quick check before they even commit their code.

The Code Review Process: This is our most critical process. I create a Pull Request (PR) Template in our Git repository. This template is a checklist that every developer must complete before submitting their code for review. It includes questions like:

Have you tested this with keyboard navigation only?

Does this have a clear and visible focus state?

Does this negatively impact the page's Lighthouse score?

Automated Pipeline Checks: I use Lighthouse CI. This tool is configured to run automatically on every pull request. If the PR causes the accessibility or performance score to drop below a set threshold (e.g., 90), the build fails, and the PR is blocked from being merged. This is our non-negotiable safety net.

Part 2: Balancing Priorities with Deadlines
This is the constant challenge in an agency, and the key is proactive communication and a "shift-left" mentality.

Shift Left: It is always cheaper and faster to build something correctly from the start than to fix it right before a deadline. By integrating the standards I just mentioned into our daily workflow, quality becomes a routine, not a last-minute emergency.

Prioritization and Triage: We can't fix everything. When a project is under pressure, we use an 80/20 approach. Automated tools are great at triaging issues by severity. We will always fix a critical accessibility blocker (like a modal that traps keyboard focus) before we address a minor color contrast issue.

Advocate and Educate: I work with Project Managers to frame these standards not as "extra time," but as "risk reduction." Good accessibility reduces legal risk. Good performance increases user conversion and SEO. I make a business case for quality, so it's seen as a core project requirement, not a "nice-to-have."

Ultimately, the goal is to make quality the path of least resistance. Our tools and processes are designed to make doing the right thing the easiest thing for the developer to do.
