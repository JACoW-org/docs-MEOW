## List of tasks

This event acquires a lock on the data before executing, renewing it after every task is completed.

Following is the list of exploited tasks:

1. [Sessions and Materials Collection](#sessions-and-materials-collection)
2. [Contributions Data Collection](#contributions-data-collection)
3. [Proceedings Data Object Creation](#proceedings-data-object-creation)
4. [Download of the Papers](#download-of-the-papers)
5. [Papers Report](#papers-report)
6. [Papers Validation](#papers-validation)

In the end, the event will return a list of dictionaries with metadata and errors.

## Sessions and Materials Collection

This task collects sessions and attachments related to the conference based on the provided information. In summary, it starts two parallel subtasks:

- `download_sessions`: Retrieves sessions related to the event from Indico and appends them to the `sessions` list.
- `download_attachments`: Retrieves attachments associated with the event from Indico and appends them to the `attachments` list.

The two lists are then returned by the task.

## Contributions Data Collection

This task collects contributions and files associated with a conference based on the provided information. In summary, it starts parallel subtasks `download_contributions` that retrieves the contributions from Indico and appends them to the `contributions` list. 

The list is then returned by the task.

## Proceedings Data Object Creation

This task builds a proceeding's object and then returns it. In particular, for each contribution, it updates the flag `is_included_in_pdf_check` to `True` if and only if that contribution has the `green` or `yellow` state in Indico.

## Download of the Papers

This task is responsible for downloading contribution papers associated with the proceedings data object. Here's an overview of what happens in this function:

1. **Extracting Papers**: It extracts a list of `FileData` objects representing contribution papers whose contributions have the `green` or `yellow` state.

2. **Download**: For each file data, in parallel, a download subtask is started, that retrieves the PDF file and caches it.

Once all files have been downloaded, the task returns a list containing two sublists: the first sublist contains the updated proceedings object, and the second sublist contains the reference to the downloaded PDFs.

## Papers Report

This task operates on the previously created proceedings object. Here's an overview of what happens:

1. **Paper Selection**: It builds a list of `ContributionPaperData` objects representing conference papers that meet the `green` or `yellow` state condition.

2. **Papers Processing**: For each paper, a subtask is initiated to perform the following steps:
   
    - **Data Collection**: The subtask collects various information from the PDF, such as text content, page details, and font information.
    - **Keyword Extraction**: It extracts keywords from the PDF's text content.
    - **Data Organization**: All the gathered information is structured into an object.

3. **Proceedings Update**: The proceedings object is updated by incorporating all the extracted information.

## Papers Validation

This task validates data based on certain criteria. Here's an overview of what it does:

1. **Initialization**: It retrieves page width and height settings from the [settings](https://purr-docs.jacow.org/Functionalities/papersCheck/#settings).

2. **Data Collection and Validation Loop**: For each contribution in the proceedings data:
    - Metadata is extracted and stored.
    - Error checks are performed:
        - Page size is checked against the specified PDF page width and height.
        - Font embedding is verified.
    - If errors are detected, they are appended to the `errors` list along with contribution details.

3. **Results**: The function returns a list containing both the collected metadata and any errors encountered during validation.


