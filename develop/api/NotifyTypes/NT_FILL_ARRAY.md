---
uid: NT_FILL_ARRAY
---

# NT_FILL_ARRAY (193)

Fills the table with the provided content.

```csharp
int tableID = 1000;
object[] column1 = new object[] { "Row 1", "Row 2" };
object[] column2 = new object[] { "C", "D" };
object[] tableContent = new object[]{column1, column2};

protocol.NotifyProtocol(193/*NT_FILL_ARRAY*/ , tableID, tableContent);
```

## Parameters

- tableID (int): ID of the table parameter.
- tableContent (object[]): Content to add. This is an object array where each element represents a column.

## Return Value

- bool: true or a null reference in case an error occurred (e.g. in case invalid data was provided).

## Remarks

- Only the provided content will be present in the table after this call. In case the provided content should only be added to the table, use the NT_FILL_ARRAY_NO_DELETE call. See NT_FILL_ARRAY_NO_DELETE (194).
- In case the column data contains null references, the corresponding cells will be cleared.
- When NT_FILL_ARRAY is used, the column type must be set to "retrieved". In case other column types are present between the specified columns (e.g. columns of type "custom"), these other columns will be skipped.
- The FillArray method cannot be used together with the "autoincrement" column option.
- From DataMiner 8.0.9 onwards (RN7351), it is possible to set an additional flag indicating that some cells should be cleared (using protocol.Clear) or preserved (using protocol.Leave). To enable this, set the second entry of the tableInfo to true as indicated below:

  ```csharp
  object tableInfo = new object[] { 10, true }; // Setting the second entry to true enables the use of the protocol.Clear and protocol.Leave functionality.
  object[] tableData = new object[4];

  tableData[0] = new object[3] { "1-Index", "2-Index", "3-Index" };
  tableData[1] = new object[3] { 10, 10, 10 };
  tableData[2] = new object[3] { protocol.Clear, protocol.Clear, protocol.Clear };
  tableData[3] = new object[3] { 30, 30, 30 };

  protocol.NotifyProtocol(193, tableInfo, tableData);
  ```

- From DataMiner 9.6.6 onwards (RN 21482), it is possible to specify a datetime that will be applied to all values passed in the parameter set. To enable this, set the third entry of tableInfo as indicated below:

  ```csharp
  object tableInfo = new object[] { 10, false, DateTime.Now };
  object[] tableData = new object[4];

  tableData[0] = new object[3] { "1-Index", "2-Index", "3-Index" };
  tableData[1] = new object[3] { 10, 10, 10 };
  tableData[2] = new object[3] { 10, 10, 10 };
  tableData[3] = new object[3] { 30, 30, 30 };

  protocol.NotifyProtocol(193, tableInfo, tableData);
  ```

- From DataMiner 9.6.13 onwards (RN 23815), a timestamp can be provided per cell to perform a history set on cell level. This is done by providing an object array containing the value and timestamp.

  Note that not all cells require a timestamp. If no timestamp is specified, DateTime.Now will be used.

  ```csharp
  int tableID = 2000;

  object[] column1 = new object[] { "Row 1", "Row 2" };
  object[] column2 = new object[] { "A", new object[] { "B" , DateTime.Now - TimeSpan.FromDays(11) } };
  object[] tableContent = new object[] { column1, column2 };

  object result = protocol.NotifyProtocol(193/*NT_FILL_ARRAY*/ , tableID, tableContent);
  ```

## See also

- FillArray