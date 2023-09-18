## List of tasks

This event acquires a lock on the data before executing, renewing it after every task is completed.

Following is the list of tasks:

1. [Sessions and Attachments Collection](#sessions-and-attachments-collection)
2. [Contributions Collection](#contributions-data-collection)
3. [Adapting Final Proceedings](#adapting-final-proceedings)
4. [Clean Static Site](#clean-static-site)
5. [Download Event Attachments](#)
6. [Download Contributions Papers](#)
7. [Download Contributions Slides](#download-contributions-slides)
8. [Read Papers Metadata](#read-papers-metadata)
9. [Validate Proceedings Data](#validate-proceedings-data)
10. [Generate Contributions References](#generate-contributions-references)
11. [Generate DOIs](#generate-dois)
12. [Build DOI payloads](#build-doi-payloads)
13. [Manage duplicates](#manage-duplicates)
14. [Write Papers Metadata](#write-papers-metadata)
15. [Generate Contributions Groups](#generate-contributions-groups)
16. [Concatenate Contributions Papers](#concatenate-contributions-papers)
17. [Generate Site Pages](#generate-site-pages)
18. [Copy Event PDFs](#copy-event-pdfs)
19. [Generate Final Proceedings](#generate-final-proceedings)
20. [Link Static Site](#link-static-site)

## Sessions and Attachments Collection

This task retrieves data related to sessions and attachments from Indico and returns it to the main task. In the subtask responsible for downloading attachments, a filtering process is applied based on conference settings.

## Contributions Collection

This task retrieves data related to the conference's contributions from Indico and returns it to the main task. To retrieve the contributions, every session is iterated.

## Adapting Final Proceedings

In this task, we create an instance of the `ProceedingData` data class to which we refer as proceedings object. The included data is as follows:

- **Editors**: These are retrieved and deserialized from the settings.
- **Sessions**: These are deserialized from the previously retrieved sessions in a list of `SessionData` objects.
- **Contributions**: These are deserialized from the previously retrieved contributions in a list of `ContributionData` objects.
- **Attachments**: These are deserialized from the previously retrieved attachments in list of `AttachmentData` object.

Sessions are sorted by their starting datetime, and those without contributions are removed. Contributions are sorted by their session datetime, session code, and contribution code.

Finally, the proceedings object is returned to the main task.

## Clean Static Site

This task removes all the previously generated folders for this specific conference from a prior main task run.

## Download Event Attachments

This task retrieves files using the list of attachments from the proceedings object and stores them under `var/run/{event_id}_tmp`.

## Download Contributions Papers

Initially, the task compiles a list of files to be retrieved by filtering publishable contributions that have a paper in their latest revision. Then, all the papers are retrieved from Indico using parallel subtasks and stored under `var/run/{event_id}_tmp`.

## Download Contributions Slides

This task is executed only for the generation of the final proceedings. Initially, the task compiles a list of files to be retrieved by filtering contributions that have slides in their latest revision. Then, all the files are retrieved from Indico using parallel subtasks and stored under `var/run/{event_id}_tmp`.

## Read Papers Metadata

Initially, this task retrieves the list of papers for which metadata needs to be read.

For each PDF file, the task retrieves the metadata, including keywords, fonts, page width and height, and the total number of pages.

All the gathered information is then collected and returned.

Finally, another subtask updates the proceeding object by iterating through the contributions and updating the following properties:

- Paper size (in bytes)
- List of keywords
- Page number (based on the total number of contributions)
- Fonts
- Page width and height

## Validate Proceedings Data

This task is responsible for validating the data that has been collected so far and returning errors to the client. To achieve this, the metadata of each contribution that can be published is checked for the following criteria:

- The page count is not null.
- The paper's width and height are correct.
- The paper's fonts are embedded.

## Generate Contributions References

This task is responsible for generating references for the contributions. The following formats are used for generating references:

- BibTeX
- LaTeX
- Word
- RIS
- EndNote

Initially, a `ContributionRef` object is constructed for each contribution. This model contains all the necessary data. Subsequently, XSLT files are utilized. For each format, there is a dedicated XSLT file capable of generating a string that contains the reference in the appropriate format. To do so, before applying the XSL transformation, a XML string of the `ContributionRef` object is generated, by using a Jinja template.
Finally, the proceeding object is updated by incorporating the generated contribution references.

## Generate DOIs

This task generates a `ContributionDOI` object for each contribution in the conference. `ContributionDOI` includes all the metadata necessary to create the DOI page for each contribution.

Additionally, a dictionary containing all the data for the conference's DOI is also generated.

Finally, the proceeding object is updated by incorporating the generated DOIs.

## Build DOI Payloads

This task is executed only during the generation of the final proceedings and is responsible for creating JSON payloads to use the datacite.org API for generating DOIs for each contribution.

To generate the JSON string, the `ContributionDOI` class includes an `as_json()` function that constructs a dictionary with all the required metadata and then converts it into a string.

The resulting JSON strings are then individually written to JSON files for future use.

The same process is done also for conference's DOI payload.

## Manage Duplicates

This task deals with contributions that have been marked as "duplicate_of." For these contributions, a `DuplicateContributionData` object is created, which contains information about the contribution to which the current one is a duplicate. This information includes the DOI URL and the dates of reception, revision, acceptance, and issuance.

## Write Papers Metadata

This task manages the writing of metadata into the PDF files of the papers. For each file, a writing task is initiated, with parallel execution.

Additionally, the task is responsible for generating the footers and headers for the pages of each paper.

## Generate Contributions Groups

This task is responsible for grouping contributions based on various attributes, including:

- Session
- Classification
- Author
- Institute
- DOI per Institute
- Keyword

These groups serve as indexing modes for contributions on the final website.

## Concatenate Contributions Papers

This task is responsible for concatenating the papers to create the following volumes:

- "Proceedings at a Glance," which includes the first pages of all the papers. Each page also serves as a hypertextual link to the respective paper.
- "Proceedings Volume," which includes all the papers of the conference.

Both volumes may have a cover if added through the attachments, and a table of contents is generated based on the settings.

To expedite the process, papers are divided into chunks (based on their page numbers) and concatenated in parallel.

The tool used to perform this task is [PDFtk](https://www.pdflabs.com/tools/pdftk-the-pdf-toolkit/).

## Generate Site Pages

This task operates in three steps:

1. An instance of `HugoFinalProceedingsPlugin` is created. Starting from the proceeding object, it initializes all the variables needed to generate the static web pages. Additionally, it also initializes the `Path` variables pointing to the folders where these pages will be stored.

2. The `run_prepare` function actively creates the folders using the `Path` variables initialized previously. It then initializes a `JinjaTemplateRenderer` object that renders the `config.toml` and `index.html` files from their respective Jinja templates.

3. The `run_build` function generates all other website pages, executing them in parallel to expedite the process.

Upon completion of this phase, all website pages will be found under the `var/run/{event_id}_src` folder.

## Copy Event PDFs

This task is responsible for copying the PDF files retrieved in one of the previous tasks to the folder `var/run/{event_id}_src/static/pdf`. These assets are also necessary to build the final website. The subtasks are as follows:

- `copy_event_attachments` for the attachments (excluding the covers).
- `copy_contribution_papers` for the PDFs of the papers.
- `copy_contribution_slides` for the slides of the contributions, but only for the final proceedings event.

## Generate Final Proceedings

Now that all the folders and files are in place, this task can run the command:

`bin/hugo --source var/run/{event_id}_src --destination out`

- `--source` instructs Hugo to use the specified folder as the source of the website, where all the generated files are stored.
- `--destination` specifies where Hugo should create the website, which, in this case, is the `out` folder.

## Link Static Site

This task is responsible for updating the preview of the proceedings website. It achieves this by moving the newly generated website to the `site_preview_path`. Once the move is complete, it deletes the website from the old folder to free up space.
