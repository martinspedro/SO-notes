# SO-notes
A bunch of useful notes for Operative Systems course, in Aveiro University

## Note taking process
1. Notes are taken using markdown syntax.
	- Check [Adam cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) or [Wikipedia](https://en.wikipedia.org/wiki/Markdown)
2. Using [pandoc](https://pandoc.org/) notes are converted to a tex file
3. Using xelatex compiler the notes in tex format are converted to pdf
4. To ease the last two steps:
	```bash
	# Generates a pdf file from a markdown file and saves it in pdf folder
	./md2pdf <notes.md>

	# Optionaly the pdf name can be given
	./md2pdf <notes.md> <pdf name>

	# Generates and preview the new pdf file obtained from the markdown
	./md2preview <notes.md>\\
	```
	
TODO:\\
- [x] CreateDisk
- [x] Showblock
- [x] rawdisk
	- [x] rawlevel
	- [x] mksofs
		- [x] computeStructure
		- [x] fillInSuperBlock
		- [x] fillInInodeTable
		- [x] fillInFreeClusterTable
		- [x] fillInRootDir
		- [x] resetClusters
	- [ ] freelists
		- [ ] soAllocInode
		- [ ] soFreeInode
		- [ ] soAllocCluster
		- [ ] soReplenish
		- [ ] soFreeCluster
		- [ ] soDeplete
