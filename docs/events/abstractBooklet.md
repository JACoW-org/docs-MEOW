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

1. Document styles are set.
2. The document index is created.
3. The document's table of contents is generated, with contributions grouped by their session.
4. The document's body is constructed by appending the contributions' abstracts in the order defined by the table of contents.
5. The ODT object is then serialized and written to a file.
