NER
=======
Named-entity recognition (NER) (also known as entity identification, entity chunking and entity extraction) is a subtask of information extraction that seeks to locate and classify elements in text into pre-defined categories such as the names of persons, organizations, locations, expressions of times, quantities, monetary values, percentages, etc.
Most research on NER systems has been structured as taking an unannotated block of text, such as this one:
Jim bought 300 shares of Acme Corp. in 2006.And producing an annotated block of text that highlights the names of entities:
[Jim]Person bought 300 shares of [Acme Corp.]Organization in [2006]Time.In this example, a person name consisting of one token, a two-token company name and a temporal expression have been detected and classified.
State-of-the-art NER systems for English produce near-human performance. For example, the best system entering MUC-7 scored 93.39% of F-measure while human annotators scored 97.60% and 96.95%.[1][2]

+----------+-------------------------------------+
| NE Type  | Examples                            |
+----------+-------------------------------------+
| LOCATION | Murray River                        |
|          | , Mount Everest                     |
+----------+-------------------------------------+
| DATE     | June                                |
|          | , 2008-06-29                        |
+----------+-------------------------------------+
| TIME     | two fifty a m                       |
|          | , 1:30 p.m.                         |
+----------+-------------------------------------+
| FACILITY | Washington Monument（华盛顿纪念碑） |
|          | , Stonehenge                        |
+----------+-------------------------------------+
| GPE      | South East Asia                     |
|          | , Midlothian                        |
+----------+-------------------------------------+

Spacy支持的NER
---------------------

'LAW',   

 'ORG',  ORGANIZATION

 'PERSON', 

 'DATE',

 'PRODUCT',

 'FAC', FACILITY

 'WORK_OF_ART',

 'GPE',

 'LOC',LOCATION

 'MONEY',

 'NORP',

 'PERCENT',

 'CARDINAL',

 'TIME',

 'LANGUAGE',

 'QUANTITY',

 'EVENT',
 
 'ORDINAL'
