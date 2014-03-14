
# Csv Parser
Quickly take in and output csv formats.

## Example usage:
```php

//
// Simple array to string usage
//
$array = [['id'=>1, 'name'=>'Bob'],['id'=>2, 'name'=>'Bill']];
$parser = new \CsvParser\Parser();
$csv = $parser->fromArray($array);
var_dump($parser->toString($csv));

//
// Full power examples:
//

// setup initial parser
$parser = new \CsvParser\Parser(',', '"', "\n");

// change settings after init
// set column delimiter
$parser->fieldDelimiter = ';';
// set text enclosure
$parser->fieldEnclosure = "'";
// set line delimiter
$parser->lineDelimiter = "\n";

// Input (returns instance of \CsvParser\Csv)
$csv = $parser->fromArray([['id'=>1, 'name'=>'Bob'],['id'=>2, 'name'=>'Bill']]);
$csv = $parser->fromString("id,name\n1,Bob\n2,Bill");
$csv = $parser->fromFile('demo.csv');

// get row count
var_dump($csv->getRowCount());

// append/prepend rows
$csv->appendRow(['id'=>3, 'name'=>'Ben']);
$csv->prependRow(['id'=>4, 'name'=>'Barry']);

// map function over column
$csv->mapColumns('name', 'trim');
$csv->mapColumns('name', function ($name) {
    return trim($name);
});

// add a column
$csv->addColumn('codename');

// map function over rows
$csv->mapRows(function ($row) {
    $row->codename = base64_encode($row->id);
});

// filter down rows
$csv->filterRows(function ($row) {
    return $row->id !== '#'; // remove rows where the id column just has a hash inside
});

// remove row by index
$csv->removeRowByIndex(4);
// or remove row(s) by column value, such as id 22
$csv->removeRow('id', 22);
// or remove row(s) by multiple creiteria, such as when id 22 AND when name is 'some name'
$csv->removeRows(['id'=>22, 'name'=>'some name']);

// Column reordering
$csv->reorderColumn('colname', 0); // move to position 0 (the start)
// or multiple
$csv->reorderColumns(['colname1'=>0, 'colname2'=>4]);

// Row reordering
// to move the row with id of 22 to the start
$csv->reorderRow('id', 22, 0);
// or move id 22 to the start, and id 5 after it
$csv->reorderRows('id', [22 => 0, 5 => 1]);

// Output
var_dump($parser->toArray($csv));
var_dump($parser->toString($csv));
var_dump($parser->toFile($csv, 'demo.csv')); // file was created?

// Need to chunk into multiple chunks/files?
$chunks = $parser->toChunks($csv, 1000);
foreach ($chunks as $i => $chunk) {
    $parser->toFile($chunk, "output-{$i}.csv");
}

```
