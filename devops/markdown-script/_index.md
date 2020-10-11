---
title: Markdown script
date: 25/09/2020 19:38
bookCollapseSection: true
path: web-services/devops/markdown-script
---
### Markdown script
{{groovy}}
import org.xwiki.environment.*
import org.xwiki.model.reference.*

if (request.confirm == '1') {
  services.query.xwql("select distinct doc.fullName from Document doc where doc.space like '5web-services' or doc.space like '5web-services.%'").execute().each() {
    print "* Converting ${it} to MD..."
    def itemDoc = xwiki.getDocument(it)
    def title = itemDoc.title
    def fn = itemDoc.fullName
    def newContent = services.rendering.render(itemDoc.getXDOM(), 'markdown/1.2')
    def tmpDir = '/tmp/md-export'
    def pathSerializer = services.component.getInstance(EntityReferenceSerializer.TYPE_STRING, 'path')
    def outputFile = new File(tmpDir, pathSerializer.serialize(itemDoc.documentReference))
    def outputFile2 = new File(outputFile.parentFile, 'attachments')
    def outputFile3 = new File(outputFile.parentFile, 'links')
    def itemUrl = xwiki.getURL(it)
    if (fn.contains('\'')) {
      fn = fn.replace('\'','\'\'')
      println "single quote replaced"
    }
    def link = xwiki.search("select link.link from XWikiLink as link where link.fullName like '${fn}'")
    outputFile.parentFile.mkdirs()
    if (link) {
      link.each {
        outputFile3 << it
        outputFile3.append('\n')
        println it
      }
    }
    if (itemDoc.getAttachmentList()) {
        def atList = itemDoc.getAttachmentList()
        atList.each {
            atName = it.filename
            atUrl = itemDoc.getAttachmentURL(atName)
            outputFile2 << atUrl
            outputFile2.append('\n')
            }
        }
    outputFile << title
    outputFile.append('\n')
    outputFile.append(newContent)
    println "Saved in ${outputFile.toString()}"
    println "This is a link ${link}"
    println "This is FN ${fn}"
  }
}

println "[[Export>>||queryString='confirm=1']]"
{{/groovy}}