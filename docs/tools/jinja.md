Jinja is a popular templating engine for Python, inspired by Django's templating system. It allows you to embed dynamic content and logic into templates, making it easier to generate dynamic HTML, XML, JSON or any other text-based format.

Jinja is extensively used in MEOW in order to generate the proceedings' site pages or the contributions' references.

Here are some key features and concepts related to Jinja's template rendering:

- **Template Tags**: Jinja uses special tags enclosed in double curly braces `{}` to render dynamic content or expressions. For example, `{{ variable_name }}` is used to insert the value of a variable into the template.
- **Variables**: You can define variables in your templates and then reference them using the `{{ variable_name }}` syntax. These variables can hold dynamic data like strings, numbers, or even complex objects.
- **Control Structures**: Jinja supports control structures like if, for, and while. You can use `{% if condition %}...{% endif %}` to conditionally render content or `{% for item in items %}...{% endfor %}` to iterate over a list or dictionary.
- **Filters**: Filters in Jinja are used to modify the output of variables or expressions. For example, you can use `{{ variable_name|filter_name }}` to apply a filter to a variable. Filters can be used for tasks like formatting dates, converting text to uppercase, or escaping HTML.
- **Template Inheritance**: Jinja supports template inheritance, allowing you to create a base template with a structure common to multiple pages and then extend it in child templates. This DRY (Don't Repeat Yourself) approach simplifies template management.
- **Macro Definitions**: Macros are reusable code blocks defined in Jinja templates. They can accept parameters and are similar to functions or methods in programming. Macros are useful for avoiding code duplication.
- **Custom Extensions**: Jinja allows you to create custom extensions or filters to extend its functionality. This can be particularly useful when you need to perform custom logic within your templates.
- **Escaping**: Jinja automatically escapes content to prevent cross-site scripting (XSS) attacks. However, you can explicitly mark content as safe to disable escaping when you trust the source.
- **Comments**: Jinja templates support comments using the `{# ... #}` syntax. Comments are not displayed in the rendered output.
- **Error Handling**: Jinja provides detailed error messages to help you diagnose and fix issues in your templates. This makes debugging easier.

In MEOW, there is a `jinja` folder that includes all the templates. In order to generate the text from a template, we define a custom renderer, that includes a custom render function, that wraps the original Jinja render and builds the dictionary of variables from a custom object.
