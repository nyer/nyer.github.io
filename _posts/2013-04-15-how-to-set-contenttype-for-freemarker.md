---
layout: default
title: how to set content-type for freemarker
---

Sometimes, we need to set the content-type of a http response, for exmaple, 
The app runtime is jsp, set the content-type as to output an excel file.
In the jsp template, it is easy, just like this:

	<%@ page contentType="application/vnd.ms-excel;charset=utf-8" %>

However, when template is a freemarker template, it is not so straight.
The freemarker doesn't provider a directive to set the content-type, 
So it is common to set the content-type with the request.

	request.setContentType("application/vnd.ms-excel;charset=utf-8")

However, when we request the action, we find that the content-type of 
the response is `text/html`, that doesn't work. What is wrong with that?
Well, i dig the freemarker servlet, and i found some source code:

	Object attrContentType = template.getCustomAttribute("content_type");
	if (attrContentType != null) {
		response.setContentType(attrContentType.toString());
	}
	else {
			//....
	}

The code above does such a work: it gets a attribute named `content_type` 
of the template, and check if the attribute exists, if it exists, then 
set the contentType with the attribute value.
Well, the problem seems to have been solved. Just add

	<#ftl attributes={"content_type" : "application/vnd.ms-excel;charset=utf-8"}

to the head of the template, and the contentType is setted successfully :).
