# SO-notes
A collection of useful notes and pdfs for [Operative Systems course](http://www.ua.pt/deti/uc/2851), at [Aveiro University](https://www.ua.pt/).

A website is also available [here](https://k3rn3l-pan1c.github.io/SO-notes/).

## Published Notes
- [Operating Systems: Introductory Concepts](pdf/OS_Book.pdf)
- [Shell Scripting](pdf/Shell_Scripting.pdf)
- [Sofs17](pdf/sofs17_Book.pdf)
- [Interprocess Communication](pdf/IPC_Book.pdf)
- [Process Management](pdf/PM_Book.pdf)
- [Theoretical Notes Only](pdf/Theoretical_SO_Book.pdf)
- [All Notes](pdf/SO_Book.pdf)

## Note taking process
 1. Notes are taken using pandoc markdown extended syntax
	- Check [Adam cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) or [Wikipedia](https://en.wikipedia.org/wiki/Markdown) for more information and useful tips
2. Using [pandoc](https://pandoc.org/), the notes are converted into a eye-candy pdf, using tex as an intermediary file type
	- The latex compiler used is xelatex
	- The [Eisvogel](https://github.com/Wandmalfarbe/pandoc-latex-template) template is used with small modifications
3. Using an yaml header, some metadata and rendering options are provided
4. Some of the used diagrams are generated using either [dot](https://en.wikipedia.org/wiki/DOT_(graph_description_language)) or [timing-tikz](https://ctan.org/pkg/tikz-timing). The remaining were copied from the course slides.

### DOT & Grphviz
The [dot graph description language](https://en.wikipedia.org/wiki/DOT_(graph_description_language)) is a graphic description language, enable the fast creation of reasonably neat diagrams

For interpreting the dot code, [graphviz](https://graphviz.gitlab.io/) as used.

For more information on dot and graphviz, check out [this amazing guide](https://www.google.pt/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&ved=0ahUKEwjUrryp4srYAhWBtxQKHWKzA68QFggzMAA&url=https%3A%2F%2Fgraphviz.gitlab.io%2F_pages%2Fpdf%2Fdotguide.pdf&usg=AOvVaw1NgHmOrdTb4E59oQvAx-jW)

### Timing-Tikz
A subset of the Latex vector graphic language, [Tikz](https://en.wikibooks.org/wiki/LaTeX/PGF/TikZ), that uses geometric/algebric description to create beatiful diagrams

[Timing-tikz](https://ctan.org/pkg/tikz-timing) is a package specific for the generation of timing diagrams, such as digital signals.

For more information, check out [this amazing guide](https://www.google.pt/url?sa=t&rct=j&q=&esrc=s&source=web&cd=5&cad=rja&uact=8&ved=0ahUKEwjm56PM5MrYAhXFWhQKHVFdCbsQFghVMAQ&url=http%3A%2F%2Fwww.bakoma-tex.com%2Fdoc%2Flatex%2Ftikz-timing%2Ftikz-timing.pdf&usg=AOvVaw0C_eTrtY9GOPjUU0uMLmM3)


## From markdown to pdf
To provide a rapid view of the output pdf, two bash script are available in the _scripts_ folder 
```bash
# Generates a pdf file from a markdown file and saves it in pdf folder
./md2pdf <notes.md>

# Optionally the pdf name can be given
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
## Disclaimer

Currently this work is **highly experimental and not yet scientific reviewed**.
**Do not trust this notes by themselves**. They are meant to complete other materials, **not replace them**

## Roadmap

- [ ] Curate Raw notes
	- [X] Operative Systems: Introductory Concepts
	- [ ] Bash Scripting 
	- [ ] Filesystems & Sofs17
	- [X] Process Management
	- [X] Interprocess Communication 
	- [ ] Memory Management
- [X] Improve the publishing scripts
- [ ] Improve the publish template
- [ ] Provide a static website with all the notes


## License
The content of this project itself is licensed under the [Creative Commons Attribution-ShareAlike 4.0 International license](https://creativecommons.org/licenses/by-sa/4.0/) and the underlying source code is under the [MIT license](https://opensource.org/licenses/mit-license.php).

