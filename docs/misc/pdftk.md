# Προσθήκη Bookmarks σε PDF

### Εξαγωγή μεταδεδομένων

```bash
pdftk input.pdf dump_data > data.txt
``` 

### Επεξεργασία μεταδεδομένων

Άνοιξε το data.txt με έναν text editor (π.χ. xed ή nano). Για να προσθέσεις σελιδοδείκτες, προσθέτεις στο τέλος του αρχείου εγγραφές αυτής της μορφής:

```Plaintext

BookmarkTitle: Τίτλος Εγγράφου 1
BookmarkLevel: 1
BookmarkPageNumber: 1

BookmarkTitle: Τίτλος Εγγράφου 2
BookmarkLevel: 1
BookmarkPageNumber: 15
```

### Εισαγωγή μεταδεδομένων

```bash
pdftk input.pdf update_info data.txt output output.pdf
```