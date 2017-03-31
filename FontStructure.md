# Font Structure


![Alt text](/img/structure.png "Font File Structure")


4 Bytes: ttId

2 Bytes: the number of tables

6 Bytes: Skip!!


테이블 수만큼 반복

{

  4 Bytes: Tag of table
  
  4 Bytes: CheckSum
 
  4 Bytes: table location1
  
  4 Bytes: table location2
  
}


// name table

Pos: table location1 + 2

2 Bytes: the number of records

2 Bytes: start Of Storage

레코드 수만큼 반복

{
  2 Bytes: platformID
  2 Bytes: platformEncodingID
  2 Bytes: languageID
  2 Bytes: nameID
  2 Bytes: length
  2 Bytes: offset
  
  if nameId == 6
    goto (table_location1 + startOfStorage + offset)
    if platformID == 0 || platformID == 3
      readUnicodeString(length)
    else
      readStandardString(length)
}

--> nameId == 4: full name

--> nameId == 1: fmaily name






