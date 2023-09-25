## List of tasks

There are a total of three tasks involved in generating the Abstract Booklet document:

1. Data collection
2. Abstract booklet data
3. ODT export

### Data collection

This task collects the data necessary for creating a conference's abstract booklet, including sessions and contributions. The data is retrieved from Indico. It starts by obtaining the list of sessions and then fetches the list of contributions for each session. The task returns the list of sessions and contributions.

### Abstract booklet data

This task takes the list of sessions and contributions as input and organizes the data into a dictionary, which is then returned.

### ODT export

This task is responsible for creating the ODT document using the OdfPy library. You can find more information about OdfPy [here](https://github.com/eea/odfpy).

The following subtasks are performed to create the document, after the ODT object is created:

#### Document Styles

This task is responsible for defining various styles used in the ODT document. These styles include styles for:

- Different levels of headings
- Tables
- Paragraphs related to authors, speakers, coauthors, descriptions, and funding agencies
- Footnotes

The task returns a dictionary containing references to each of the defined styles. These styles can be applied to various elements within the ODT document to control their appearance.

#### Abstract Booklet Index

This task is responsible for creating an index in the form of a dictionary that contains information about the abstract booklet's content. It follows these steps during execution:

- For each session, it generates a unique `uuid` and captures the `code` and `title`.
- For each contribution within a session, it generates another unique `uuid` and extracts the `code` and `title`.

The resulting index is a valuable resource for efficiently accessing and referencing information about sessions and contributions within the abstract booklet.

#### Abstract Booklet Table of Contents

This task is responsible for generating a table of contents (TOC) within the ODT document for an abstract booklet.

The task performs the following key actions:

- Initializes a TOC structure within the ODT document.
- Sets up templates and styles for various heading levels (h1, h2, h3).
- Defines the appearance and structure of TOC entries.
- Adds the TOC to the document.

The TOC is then returned by the task.

#### Abstract Booklet Body

This task generates the body of the abstract booklet based on the settings. It customizes the formatting and content of sessions and contributions. The key settings used include:

- `ab_session_h1` and `ab_contribution_h1`: These settings define the format for session and contribution headers, allowing you to insert dynamic information such as session or contribution codes, titles, start times, and end times.

- `ab_session_h2` and `ab_contribution_h2`: Similar to the above settings, these allow for alternative header formats, useful for differentiating between session and contribution styles.

##### Custom Fields

You can include custom fields in the abstract booklet using the `custom_fields` setting. This setting lets you specify the names of custom fields you want to include in the booklet. The task checks for these fields in the contributions and adds them to the generated content.

##### Document generation

The task iterates through the session and contribution data and uses the configured [settings](https://purr-docs.jacow.org/Functionalities/abstractBooklet/#settings) to format the headers and content. Here's how it works:

- For each session:
  - It dynamically replaces placeholders in the session header templates (`ab_session_h1` and `ab_session_h2`) with actual session data (code, title, start, and end times).
  - It creates bookmarks for easy reference and adds session chair information if available.
  - It adds a black line for separation.
  - It adds the session's description.
  - If custom fields are specified, it includes them in the session content.

- For each contribution within a session:
  - It dynamically replaces placeholders in the contribution header templates (`ab_contribution_h1` and `ab_contribution_h2`) with actual contribution data (code, title, start, and end times).
  - It creates bookmarks for easy reference.
  - It adds speaker, primary author, and coauthor information based on the configured styles.
  - It includes a description of the contribution.
  - If custom fields are specified, it includes them in the contribution content.

The task then returns the ODT document.



In the end, the event serializes the generated ODT document into a file.
