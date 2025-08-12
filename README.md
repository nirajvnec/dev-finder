function getExcelColumnName(columnNumber: number): string {
        let columnName = '';
        while (columnNumber >= 0) {
          columnName = String.fromCharCode(65 + (columnNumber % 26)) + columnName;
          columnNumber = Math.floor(columnNumber / 26) - 1;
        }
        return columnName || 'A'; // Default to 'A' if no valid column
      }

      // Dynamically generate column headers beyond Z1 with hidden notes
      columnKeys.forEach((header, index) => {
        const colLetter = getExcelColumnName(index); // Generates A, B, ..., Z, AA, AB, etc.
        ws[`${colLetter}1`] = {
          v: header, // Set header value
          t: 's', // Type 's' for string
          c: [{ a: 'Author', t: `Note for ${header}` }] // Comment added
        };
        ws[`${colLetter}1`].c.hidden = true; // Hides the comment, visible on hover (confirmed working)
      });