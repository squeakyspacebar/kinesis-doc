This is the repository for housing/generating the documentation for Kinesis.

## Notes and Instructions

### Rebuild Table of Contents

This process uses:
- [GNU sed](https://www.gnu.org/software/sed/)

1. Grab the HTML sidebar nav list generated by Hugo and convert it into markdown; use the `hugo` command if you're unsure whether you have the latest version.
2. Replace the existing ToC hierarchy in `content/00-table-of-contents.md` with the newly generated one and save the changes to file.
> **Note:** Don't forget to remove the top Table of Contents link.
3. Run the following regex processing and write the results back to file:
```
indent=4;
sed -r "s/\s{$indent}/  /g" content/00-table-of-contents.md \
  | sed -r 's/\*\s+\*\s+/  - /g' \
  | sed -r 's/\*\s+/- /g' \
  | sed -r 's/\/kinesis-doc\///g' \
  | tee content/00-table-of-contents.md content/_index.md
```
4. Rebuild the site with the `hugo` command.

### Build PDF

This process uses:
- [GNU sed](https://www.gnu.org/software/sed/)
- [Pandoc](https://pandoc.org/)

1. Create temporary working and build output directories:
    `mkdir -p .working .build`

2. Create shortcut to allow access to images:
    `ln -s ${PWD}/assets/images .working/images`

3. Copy markdown files to working directory:
    `cp content/[^_]*.md .working`

4. Enter working directory (pandoc runs from the current working directory):
    `cd .working`

5. Process each markdown files, convert them to HTML files, insert page break elements, then create the PDF:
   ```
   FILES=([^_]*.md)
   FILECOUNT=${#FILES[@]}
   LASTFILE=${FILES[${#FILES[*]}]}
   for f in "${FILES[@]}"
   do
   sed -i -E 's/([0-9]{2}-[^#]*)//g' ${f}
   OUTPUT_FILENAME="`basename "${f}" .md`.html"
   pandoc ${f} -f markdown-implicit_figures -t html -o $OUTPUT_FILENAME
   if [[ ${f} != $LASTFILE ]]
   then
   echo "${f} is not $LASTFILE"
   echo '<div style="page-break-after: always"></div>' >> $OUTPUT_FILENAME
   fi
   done
   pandoc *.html -t html5 --css ../docgen/print.css -o ../.build/kinesis-doc.pdf
   ```

### Build API Documentation

This process requires:
- [Doxygen](https://www.doxygen.nl/)

1. Remove the old generated documentation, generate the new doxygen documentation site, then remove the JavaScript files (this is specifically to comply with Unity Asset Store rules):
```
UNITY_SCRIPTS_DIR="../path/to/kinesis/scripts/"
OUTPUT_DIR="static"
rm -r static/api
(cat docgen/Doxyfile; echo "INPUT=${UNITY_SCRIPTS_DIR}"; echo "OUTPUT_DIRECTORY=${OUTPUT_DIR}") \
| doxygen -
rm static/api/*.js
```
