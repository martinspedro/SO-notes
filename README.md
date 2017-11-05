# SO-notes
A bunch of useful notes for Operative Systems course, in Aveiro University

## Note taking process
1. Notes are taken using markdown syntax
	- Check [Adam cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) or [Wikipedia](https://en.wikipedia.org/wiki/Markdown) for more information and useful tips
2. Using [pandoc](https://pandoc.org/) notes are converted to a tex file
3. Using xelatex compiler the notes in tex format are converted to pdf
4. To ease the last two steps, two bash scripts were created:
	```bash
	# Generates a pdf file from a markdown file and saves it in pdf folder
	./md2pdf <notes.md>

	# Optionaly the pdf name can be given
	./md2pdf <notes.md> <pdf name>

	# Generates and preview the new pdf file obtained from the markdown
	./md2preview <notes.md>
	```

---
	
## TODO:
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

---

Soon a website will be available with all the notes. Until then you can check the [repository](https://github.com/k3rn3l-pan1c/SO-notes) for the notes.
