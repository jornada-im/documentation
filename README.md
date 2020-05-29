# Jornada LTER IM documentation pages

/docs contains the content

not sure yet where these pages will end up.

## standards

A directory of documents about best practices and standards for metadata creation at Jornada Basin LTER.

Please refer to these as you are packaging data and creating EML documents for Jornada Basin research products.

**Edit as you see fit, but please track your changes when possible and Greg will try to sync with the GitHub version of this repository.**

### Building the standards document

    pandoc JRN_metadata_standards.md --toc --metadata date="$(date +%Y-%m-%d%n)" -o ../JRN_metadata_standards.docx 

