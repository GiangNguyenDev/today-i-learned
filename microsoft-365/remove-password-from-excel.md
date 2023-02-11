# How to remove a password from a protected Excel worksheet

- Make a backup copy of your spreadsheet
- Rename the extension of your spreadsheet from *.xlsx to *.zip
- Open the ZIP file in any File Compression software, e.g., 7-Zip
- Locate the `xl/worksheets` folder. Inside you will see a list of all your worksheets within your spreadsheet. They will be listed as sheet1.xml, sheet2.xml and so on.
- Open the file in Notepad and search for the line that begins with `<sheetProtection algorithmName="SHA-512″ hashValue=`. It will look something like:
```xml
<sheetProtection algorithmName="SHA-512" hashValue="x9RyFM+j9H4J3IFFhsHo3q1kQkuLydpJlLh2mdvfvk15He/Yps8xizWt/XkAJ//g+TyqgcU+8o1QBjQvKDqIzg==" saltValue="57YXDPnVjawU5s1nGyT8fQ==" spinCount="100000″ sheet="1″ objects="1″ scenarios="1"/>
```
- Select this entire line – everything between and including the `<` and `>` characters and delete it.
- Save your modified xml file. Repeat this process for every xml file in your spreadsheet.
- Rename your Zip file back to xlsx.