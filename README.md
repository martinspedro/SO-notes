# SO-notes
A bunch of useful notes for Operative Systems course, in Aveiro University

## Note taking process
 1. Notes are taken using pandoc markdown extended syntax
	- Check [Adam cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) or [Wikipedia](https://en.wikipedia.org/wiki/Markdown) for more information and useful tips
2. Using [pandoc](https://pandoc.org/), the notes are converted into a eye-candy pdf, using tex as an intermediary filetype
	- The latex compiler used is xelatex
	- The [Eisvogel](https://github.com/Wandmalfarbe/pandoc-latex-template) template is used with small modifications
3. Using an yaml header, some metadata and rendering options are provided

## From markdown to pdf
To ease the rendering process, two bash script are available
	```bash
	# Generates a pdf file from a markdown file and saves it in pdf folder
	./md2pdf <notes.md>

	# Optionaly the pdf name can be given
	./md2pdf <notes.md> <pdf name>

	# Generates and preview the new pdf file obtained from the markdown
	./md2preview <notes.md>

	```
 
## Publishing
To ease the publish of an eye-candy pdf:
```bash
	 # Assuming that exists a metadata file named notes.yaml in the metadata folder
	./renderpdf <notes.md>

	# Assuming a metadata file is going to be provided
	./renderpdf <notes.md> <metadata_file.yaml>
	
```
	
## Roadmap

[ ] Curate Raw notes
	[ ] Sofs17
	[ ] Process Management
	[ ] Interprocess Communication 
	[ ] Memory
[ ] Improve Publish Scripts
[ ] Improve Publish Template
[ ] Provide a static website with all the notes

---

Soon a website will be available with all the notes. 

Until then you can check the [repository](https://github.com/k3rn3l-pan1c/SO-notes) for the notes.
