# Parsing trello boards to csv

1. From Trello board go to

      Menu > More > Print and Export > Export as JSON

    and download the JSON to a local machine.

2. Parse the raw JSON to a simplified tabular JSON file that can be converted to csv. 

    * Download the [Trello JSON parser code](https://gist.github.com/gremau/290ab394c9bf2d6d03c18444ff60c225) and make executable some where on local machine.
    * Run `trelloparse {trello JSON file.json} output.json`

3. Convert output.json to .csv using <https://json-csv.com> 
