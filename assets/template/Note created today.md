---
created: {{date:YYYY-MM-DD}}T{{time:HH:mm:ss}}
updated: <% tp.file.last_modified_date("YYYY-MM-DD HH:mm:ss") %>  
---
# Notes created today

```dataview  
List FROM ""  
WHERE file.cday = date("<%tp.date.now('YYYY-MM-DD')%>")  
SORT file.ctime asc
```
# Notes last Touches Today

```dataview  
List  
FROM ""  
WHERE file.mday = date("<%tp.date.now('YYYY-MM-DD')%>") SORT file.mtime asc
```

# Today I learned 🧭


# Summary 💬


# Gratitude and Recognition 😍😍