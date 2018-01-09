# SO-notes
A collection of useful notes and pdfs for [Operative Systems course](http://www.ua.pt/deti/uc/2851), at [Aveiro University](https://www.ua.pt/).

A website is also available [here](https://k3rn3l-pan1c.github.io/SO-notes/).

## Published Notes
- [Shell Scripting](pdf/Shell_Scripting.pdf)
- [Sofs17](pdf/Sofs17_Book.pdf)
- [Interprocess Communication](pdf/IPC_Book.pdf)
- [Process Management](pdf/PM_Book.pdf)
- [Theoretical Notes Only](pdf/Theoretical_SO_Book.pdf)
- [All Notes](pdf/SO_Book.pdf)

## Note taking process
 1. Notes are taken using pandoc markdown extended synt ax
	- Check [Adam cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) or [Wikipedia](https://en.wikipedia.org/wiki/Markdown) for more information and useful tips
2. Using [pandoc](https://pandoc.org/), the notes are converted into a eye-candy pdf, using tex as an intermediary filetype
	- The latex compiler used is xelatex
	- The [Eisvogel](https://github.com/Wandmalfarbe/pandoc-latex-template) template is used with small modifications
3. Using an yaml header, some metadata and rendering options are provided
4. Some diagrams are generated using dot or tikz

## From markdown to pdf
To provide a rapid view of the output pdf, two bash script are available in the _scripts_ folder
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

- [ ] Curate Raw notes
	- [ ] Bash Scripting 
	- [ ] Filesystems & Sofs17
	- [X] Process Management
	- [X] Interprocess Communication 
	- [ ] Memory Management
- [X] Improve the pulishing scripts
- [ ] Improve the publish template
- [ ] Provide a static website with all the notes


## License
The content of this project itself is licensed under the [Creative Commons Attribution-ShareAlike 4.0 International license](https://creativecommons.org/licenses/by-sa/4.0/) and the underlying source code is under the [MIT license](https://opensource.org/licenses/mit-license.php).

