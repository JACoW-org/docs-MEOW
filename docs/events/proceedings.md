## Filtering Function

An important callback function is defined within the event function: `filter_published_contributions`. This function is employed to filter contributions, and the filtering process plays a crucial role in distinguishing between the generation of final and pre-press proceedings.

Here's how the filtering of contributions differs:

- For final proceedings, only contributions in the `green` state that have received QA approval are included.
- For pre-press proceedings, only contributions in the `green` state are included.

## List of tasks

This event acquires a lock on the data before executing, renewing it after every task is completed.

Following is the list of tasks:

1. [Sessions and Materials Collection](#sessions-and-materials-collection)
2. [Contributions Collection](#contributions-collection)
3. [Proceedings Adapting](#proceedings-adapting)
4. [Static Site Cleaning](#static-site-cleaning)
5. [Conference Materials Download](#conference-materials-download)
6. [Contribution Papers Download](#contribution-papers-download)
7. [Contribution Slides Download](#contribution-slides-download)
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
19. [Generate Proceedings](#generate-proceedings)
20. [Link Static Site](#link-static-site)

## Sessions and Materials Collection

This task is responsible for retrieving session and material data from Indico and providing it to the main task. Since a conference may contain more materials than necessary for the proceedings generation, the task filters the materials obtained from Indico, retaining only those specified in [PURR settings](https://purr-docs.jacow.org/Functionalities/finalProceedings/#materials).

## Contributions Collection

This task collects contributions and files associated with a conference based on the provided information. In summary, it starts parallel subtasks `download_contributions` that retrieves the contributions from Indico and appends them to the `contributions` list. 

The list is then returned by the task.

## Proceedings Adapting

In this task, a proceedings object is constructed and subsequently returned. The primary objective of this task is to consolidate all the data into a single data structure and deserialize the data from the preceding tasks into the appropriate structures.

Additionally, it updates the following flags for each contribution:

- `is_included_in_proceedings` is set to `True` when the contribution is in the `green` state and has received `qa_approval`.
- `is_included_in_prepress` is set to `True` when the contribution is in the `green` state.

These flags serve as one of the primary distinctions between final and pre-press proceedings. Their values are utilized throughout the generation process to determine whether or not to include a contribution.

## Static Site Cleaning

This task is responsible for cleaning up and removing temporary files and directories associated with the static site generation process of a previous run for this conference.

## Conference Materials Download

This task retrieves files using the list of materials from the proceedings object and stores them under a temporary folder.

## Contribution Papers Download

This task is responsible for downloading contribution papers associated with the proceedings data object. Here's an overview of what happens in this function:

1. **Extracting Papers**: After filtering the contributions using the filtering function, this task extracts a list of `FileData` objects that reference PDF papers in Indico.

2. **Download**: For each file data, a download subtask is initiated in parallel to retrieve the PDF file and cache it.

Once all files have been downloaded, the task returns a list containing two sublists: the first sublist contains the updated proceedings object, and the second sublist contains references to the downloaded PDFs.

## Contribution Slides Download

This task is executed only for the generation of the final proceedings. Initially, the task compiles a list of files to be retrieved by filtering contributions that have slides in their latest revision. Then, all the files are retrieved from Indico using parallel subtasks and stored under a temporary folder.

## Read Papers Metadata

This task initially retrieves the list of papers for which metadata needs to be read.

For each PDF file, the task fetches metadata, including keywords, fonts, page width, and height, along with the total number of pages.

All the gathered information is then collected and returned.

Subsequently, another subtask updates the proceedings object by iterating through the contributions and updating the following properties:

- Paper size (in bytes)
- List of keywords
- Page number (based on the total number of contributions)
- Fonts
- Page width and height

## Validate Proceedings Data

This task is responsible for validating the data that has been collected thus far and returning errors to the client. To accomplish this, the metadata of each contribution is examined against the following criteria:

- The page count is not null.
- The paper's width and height are correct.
- The paper's fonts are embedded.

While errors do not obstruct the execution, they will be logged for the client's reference.

## Generate Contributions References

This task is responsible for generating references for the contributions. The following formats are used for generating references:

- BibTeX
- LaTeX
- Word
- RIS
- EndNote

The task builds a XML string for each contribution, that includes all the metadata that is needed to generate all the references. Subsequently, for each format, there is a dedicated XSLT file that is capable of building the reference string in the appropriate format. 
Finally, the proceeding object is updated by incorporating the generated contribution references.

### About XSLT

XSLT is an acronym for **Extensible Stylesheet Language Transformations**. It is a language primarily used for transforming and rendering XML documents. XSLT is designed to convert XML data into various formats, such as HTML, plain text, or even other XML documents.

Here is a list of key points about XSLT:

- **Transformation**: XSLT is used to transform XML documents into other XML documents or different formats, defining rules for how the transformation should occur.

- **Templates**: XSLT operates by applying templates, which are patterns that match elements or nodes in the source XML document. Templates specify how matched parts should be transformed in the output.

- **XPath**: XPath is used within XSLT to navigate and select nodes within XML documents, allowing precise control over which parts should be transformed and how.

- **Output Formatting**: XSLT lets you control the formatting of the output document, including specifying the structure, attributes, and data presentation.

- **Recursive Processing**: XSLT supports recursive processing, enabling the same transformation logic to be applied to different sections of hierarchical XML data.

- **Platform-Independent**: XSLT is platform-independent and widely supported by various programming languages and tools.

- **Common Use Cases**: XSLT is commonly used for converting XML data into HTML for web display, generating reports, and transforming XML data for integration with other systems.

XSLT is a valuable tool for transforming XML data into different formats, making it suitable for various web development, document processing, and data integration tasks.

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

This task deals with contributions that have been marked as `duplicate_of`. For these contributions, a `DuplicateContributionData` object is created, which contains information about the contribution to which the current one is a duplicate. This information includes the DOI URL and the dates of reception, revision, acceptance, and issuance.

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

Both volumes may have a cover if added through the materials, and a table of contents is generated based on the settings.

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

- `copy_event_materials` for the materials (excluding the covers).
- `copy_contribution_papers` for the PDFs of the papers.
- `copy_contribution_slides` for the slides of the contributions, but only for the final proceedings event.

## Generate Proceedings

Now that all the folders and files are in place, this task can run the command:

`bin/hugo --source var/run/{event_id}_src --destination out`

- `--source` instructs Hugo to use the specified folder as the source of the website, where all the generated files are stored.
- `--destination` specifies where Hugo should create the website, which, in this case, is the `out` folder.

## Link Static Site

This task is responsible for updating the preview of the proceedings website. It achieves this by moving the newly generated website to the `site_preview_path`. Once the move is complete, it deletes the website from the old folder to free up space.
