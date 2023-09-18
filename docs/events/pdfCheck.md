## List of tasks

This event acquires a lock on the data before executing, renewing it after every task is completed.

Following is the list of exploited tasks:

1. [Sessions and Attachments Collection](#sessions-and-attachments-collection)
2. [Contributions Data Collection](#contributions-data-collection)
3. [Proceedings Data Object Creation](#proceedings-data-object-creation)
4. [Download of the Papers](#download-of-the-papers)
5. [Text Extraction](#text-extraction)
6. [Papers Validation](#papers-validation)

In the end, the event will return a list of dictionaries with metadata and errors.

## Sessions and Attachments Collection

This task collects the data about sessions and attachments of the conference. The data is retrieved from Indico. Sessions and attachments are then returned by the task.

## Contributions Data Collection

This task retrieves each contribution's data for every session in the conference from Indico and then returns it.

## Proceedings Data Object Creation

This task builds a proceeding's object and then returns it. In particular, for each contribution, it updates the flag "is_included_in_pdf_check" to True if and only if that contribution has the "accepted" state in Indico.

## Download of the Papers

This task takes in input the previously created proceedings object and a callback function that returns True when the contribution has the "is_included_in_pdf_check" flag set to True. It will filter the conference's contributions, keeping those ones for which the previously mentioned callback returns True and if in the latest revision of that contribution there is a file of type 'paper'. For each of those contributions, it will download and save the paper. The task waits until all the files have been downloaded before completing.

## Text Extraction

This task takes input the proceedings object again and the callback function. The conference's contributions will be filtered with the same criteria as above, creating a ContributionPaperData that includes the contribution data and the paper. Then, for every one of these contributions, a subtask is launched that is performing some analysis on the paper, such as keyword extraction (more on this TODO) and document properties (width, height).

## Papers Validation

The last task performs the effective validation. At first, the desired width and height are extracted from the settings. Then, every page of every paper is iterated and the following validations are done:

- The page's width and height are checked.
- The page font is checked.

All errors are added to a list of errors that tracks every contribution whose paper contains some error, which is then returned by the task.
