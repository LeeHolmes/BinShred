# BinShred
Binary Parsing in PowerShell

BinShred is a simple binary parsing engine for PowerShell that leverages a custom .bst ("BinShred Template") syntax to drive this parsing.

A quick demonstration of this approach:

```
	Consider a simple example of the following binary content:
	
	PS C:\> Format-Hex words.bin

			   Path: C:\words.bin

			   00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F

	00000000   4C 48 02 00 00 00 05 00 00 00 48 65 6C 6C 6F 05  LH........Hello.
	00000010   00 00 00 57 6F 72 6C 64                          ...World
	
	From either documentation or investigation, we've determined that the file
	format has two main portions: a header, followed by a list of words. The
	header itself has 2 bytes in ASCII as the magic signature, followed by an
	integer representing the number count of the number of words. After that,
	each word entry has an integer representing the word length, followed by a
	word (of that length) in UTF8.
	
	A BinShred Template (.bst) for this file looks like this:
	
		header :
			magic (2 bytes as ASCII)
			wordCount (4 bytes as UINT32)
			words (wordCount items);
		words :
			wordLength (4 bytes as UINT32)
			word (wordLength bytes as UTF8);	
	
	Regions are identified as words followed by a colon. Within a region, you
	identify properties by writing their property names followed by the length
	and data type of that property. A semicolon identifies the end of a region.
	
	When you supply this template to the ConvertFrom-BinaryData cmdlet, the resulting
	object represents the data structures contained in that binary file as
	objects.
	
	PS > binshred -Path .\words.bin -TemplatePath .\wordParser.bst

	Name                           Value
	----                           -----
	magic                          LH
	wordCount                      2
	words                          (...)

	PS > (binshred -Path .\words.bin -TemplatePath .\wordParser.bst).Words[0]

	Name                           Value
	----                           -----
	wordLength                     5
	word                           Hello

```

For more detailed information, see [about_binshred.txt](https://github.com/LeeHolmes/BinShred/blob/main/PowerShellModule/about_binshred.txt).

For complicated data structures, be sure to investigate [Kaitai Struct](https://kaitai.io/), which is much more fully featured and robust.
