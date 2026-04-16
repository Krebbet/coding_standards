# Jupyter Notebook Guidelines

We provide both general and standards for the following use-cases

1. Tips for using jupyter as a rapid development tool.
2. Standards that are geared to generating notebooks as a deliverable to the client. They stress ease of use and readability in order to facilitate better client understanding and engagement. 

# General Notebook Standards

- Make sure that all code in the notebook abides by the [SFL Code Guidelines](Code%20Standards%20bb20e0f77b0f4d52b8d416a19ef1699b.md).
- Put all notebook helper functions in shared python files in the src folder. This way, there is no need to keep track of the same function in different notebooks.
- Add docstring descriptions to all functions in notebooks and src code.

# Developer Notebooks

## Useful Commands

| Update modules automatically | `%autoreload` |
| --- | --- |
| Time code | `%timeit` |
| Force high-resolution figures | `%config InlineBackend.figure_format = 'retina'` |
| Run terminal commands | `!<command>` |
| Change pandas DataFrame sizes | `# Assuming import pandas as pd
pd.set_option('display.max_rows', 500)
pd.set_option('display.max_columns', 500)
pd.set_option('display.width', 1000)` |

## Other Development Tips

“**I want to pretend to have a file but not save anything”** 

Instead of writing to a file, use `StringIO` or other methods from the `io` module

**“I keep running out of memory/time”**

Consider shortening your notebook, saving to intermediate checkpoint files or subsetting your data to a smaller sample. You can also process large CSVs or other files in chunks such that only a small portion of the data is in memory at any time.

**“I can’t see my plots/My plots are overwriting each other”**

Add `plt.show()` to the end of any cell you’d like a plot to display upon execution (assumes existences of `import matplotlib.pyplot as plt`). Otherwise, they’ll be generated in the background, often all on the same axes if you haven’t defined them explicitly.

# Client-Facing Notebooks

## Naming Conventions

Consider a standard, sortable naming convention for all notebooks, e.g. `1.0_initial_data_exploration`

It is better to omit the use of individual names and initials. Make the doc titles standardized and informative, but without references to individuals. A bad example would be `1.0-jcq-initial-data-exploration`. 

## Organization

- There should be a well written introduction to the notebook. This should include the objective of the notebook and an outline of a methodology.
- Markdown should be used to appropriately format the notebook to make it as readable as possible.

![Untitled](Jupyter%20Notebook%20Guidelines/Untitled.png)

- For longer notebooks, include a Table of Contents (or break into several standalone notebooks). See below for guides to create table of contents:
    - **The recommended**: use a `markdown` cell on the top of the notebook to create links and anchors for each section header. This might be tedious but will ensure clients can jump to the part they are interested in without worrying about enabling extension or opening it in `JupyterLab`. [https://stackoverflow.com/questions/11948245/markdown-to-create-pages-and-table-of-contents](https://stackoverflow.com/questions/11948245/markdown-to-create-pages-and-table-of-contents)
    - Since `JupyterLab` 3.0, the Table of Contents extension is built-in. However, it is not guaranteed that the clients will open the notebook in `JupyterLab`
    - Use `jupyter-nbextensions` `ToC2` for traditional notebook: [https://jupyter-contrib-nbextensions.readthedocs.io/en/latest/nbextensions/toc2/README.html](https://jupyter-contrib-nbextensions.readthedocs.io/en/latest/nbextensions/toc2/README.html) (similar to the builtin one, but able to output the ToC to `html`
    - More info can be found in this post: [https://stackoverflow.com/questions/21151450/how-can-i-add-a-table-of-contents-to-a-jupyter-jupyterlab-notebook](https://stackoverflow.com/questions/21151450/how-can-i-add-a-table-of-contents-to-a-jupyter-jupyterlab-notebook)

![Untitled](Jupyter%20Notebook%20Guidelines/Untitled%201.png)

- Each section should have its own purpose, and also have a few sentences describing any necessary background and conclusions from the results.

![Untitled](Jupyter%20Notebook%20Guidelines/Untitled%202.png)

- A conclusion is sometimes necessary, summarizing findings and adding any todos or extensions as relevant to the project.

![Untitled](Jupyter%20Notebook%20Guidelines/Untitled%203.png)

- Any setup necessary for running the notebook should be stated after or during the introduction; make sure the instructions are clear and detailed. This can also be a reference to a more detailed setup description in the `README`.

## Coding

- For longer functions, place them in a module. Do not have a wall of code in the notebook.

![Untitled](Jupyter%20Notebook%20Guidelines/Untitled%204.png)

- Do not install packages in the notebook; this should be done at the dockerization level.
- Do not use shell commands in notebooks. If shell commands are necessary, they can be placed in their own file, and called in the notebook through the subprocess library.
- Do not leave any unused code - commented or otherwise - in the notebook.
- **Make sure to run the notebook from top to bottom** before pushing to clients to ensure there are no issues. If runtime is an issue, then checkpointing and running chunks of the notebook at a time is also appropriate. Where applicable, add in information about how long the used should expect a cell to run.

## Plotting and Results

- Make sure that every plot has appropriate labeling and context. For notebooks, this will always include labeling the x- and y-axis, and almost always necessitate a title and a legend. For many plots, a descriptive take-away message is necessary after the plot to tell the client what they should be focusing on.
- Every important result should also be given context, telling the reader what the significance of the result is, preferably in the cell right after the displayed result.
- It is recommended (but not required) to use SFL Standards for plotting. Place a SFL default `matplotlibrc` files to the current working directory, typically under `/notebooks` or `/src/plotting`. You can also utilize the default function available [here](Plotting%20Guidelines/Python%20Plotting%20Defaults%20b765c6f0c02540809cb25923fab176d8.md) to set the defaults without any extra files.

![Untitled](Jupyter%20Notebook%20Guidelines/Untitled%205.png)