# Code Hosting (Coming soon...)

The workspace provides a familiar, industry-standard development experience.

#### Integrated Development Environment (IDE)

* JupyterLab Interface: A full-featured Jupyter interface supporting Notebooks, Terminal, and Markdown editors.
* File Management: Drag-and-drop file uploads or mount external storage buckets via the Settings menu.

#### Version Control

* Git Integration: The module features a built-in Git client.
  * Link your GitHub or GitLab account in User Settings.
  * Use the Git Panel on the left sidebar to Stage, Commit, and Push changes to your repositories.

#### Dataset Integration

*   Use the Data Mount feature to securely attach datasets from the DeLong Data Market without downloading them locally.

    Python

    ```
    # Example: Loading a mounted confidential dataset
    import delong_datasets
    ds = delong_datasets.load("/mnt/data/genbank-secure-v1")
    ```
