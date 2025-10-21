# Jornada LTER IM documentation pages

**WARNING** - This site is deprecated! Please visit <https://jornada-im.github.io> to find this documentation

/docs contains documentation of the IM system

/standards contains data and metadata standards used for publishing JRN data

/reports contains periodic reports from the IM system

not sure yet where these pages will end up.

## standards

A directory of documents about best practices and standards for metadata creation at Jornada Basin LTER.

Please refer to these as you are packaging data and creating EML documents for Jornada Basin research products.

**Edit as you see fit, but please track your changes when possible and Greg will try to sync with the GitHub version of this repository.**

### Building the standards document

    pandoc JRN_metadata_standards.md --toc --metadata date="$(date +%Y-%m-%d%n)" -o ../JRN_metadata_standards.docx

### Building annual reports

    pandoc JRN_IM_annual_report_2020.md --toc --metadata date="$(date +%Y-%m-%d%n)" -o ../JRN_IM_annual_report_2020.docx


