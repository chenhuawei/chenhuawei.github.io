---
layout: post
title:  "HttpClient 4.5.7使用方法"
date:   2019-04-23 14:00:52 +0800
categories: java
tags:
- java
- HttpClient
---


{% highlight java %}
    private static CloseableHttpClient httpclient = HttpClients.createDefault();
    public static String post(String url, String jsonBody) throws IOException {
        CloseableHttpResponse response = null;
        try {
            HttpPost httpPost = new HttpPost(url);
            httpPost.setHeader("Content-Type", "application/json;charset=UTF-8");
            StringEntity stringEntity = new StringEntity(jsonBody, "utf-8");
            stringEntity.setContentEncoding("UTF-8");
            stringEntity.setContentType("application/json");
            httpPost.setEntity(stringEntity);
            response = httpclient.execute(httpPost);
            HttpEntity entity = response.getEntity();
            String result = EntityUtils.toString(entity, "UTF-8");
            return result;
        } finally {
            HttpClientUtils.closeQuietly(response);
            // httpclient不需要关闭
        }
    }
    
{% endhighlight %}

