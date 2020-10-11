---
title: ExportLinks
date: 25/09/2020 19:38
bookCollapseSection: true
path: web-services/devops/exportlinks
---
### ExportLinks
{{velocity}}
|=Link|=Source
#foreach ($item in $xwiki.search("select link.link, link.fullName from XWikiLink as link"))
|$item.get(0)|$item.get(1)
#end
{{/velocity}}