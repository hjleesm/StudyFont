# Font Structure

4 Bytes: ttId
2 Bytes: the number of tables
6 Bytes: Skip!!

테이블 수만큼 반복
{
  4 Bytes: Tag of table
  4 Bytes: Skip!!!
  4 Bytes: table location1
  4 Bytes: table location2
}

// name table
Pos: table location1 + 2
2 Bytes: the number of records
2 Bytes: start Of Storage
레코드 수만큼 반복
{
  
}
