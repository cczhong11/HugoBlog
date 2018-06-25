---
title: "Create a thumbnail of photo using Java"
date: "2018-06-22"
description: "This blog will teach you how to create thumbnail in Java."
categories:
    - "Jave"
    - "Cloud computing"
    - "Image"
---

# Create a thumbnail of photo using Java
## Why we need a thumbnail

In the mobile application, if we want to show a picture quickly and we do not care the clarity of that image. We could create a thumbnail of that image, which is a smaller version of this image. The original image captured by iPhone X could be 10 MB, but the thumbnail only have 50 kb, which is much more handy to use when you need to show them quickly.

## How to create a thumbnail and integrate with a moblie app in Java Servlet

In our hackathon project, we use ionic 3 to create a native iOS app. What we could get from the phone camera is a Base64 encoded string data. Then, I need to create a thumbnail for it and save it as records in cosmosDB.

In the front end, we posted photo data as RAW to the backend. Therefore, we needed to read data from `HttpServletRequest` in the first place. Then we need to parse data to `BufferedImage` format, after that, using `org.imgscalr.Scalr` to get a resized photo. Next, encoding it back to Base64 since it is easier to show in the front end.

```java

BufferedReader r = request.getReader()
String data = r.lines().collect(Collectors.joining(System.lineSeparator()));


BufferedImage image = ImageIO.read(new ByteArrayInputStream(javax.xml.bind.DatatypeConverter.parseBase64Binary(data)));
BufferedImage thumbImg = Scalr.resize(image, Method.ULTRA_QUALITY,
                    Mode.AUTOMATIC, 200, 200);

ByteArrayOutputStream baos = new ByteArrayOutputStream();
ImageIO.write(thumbImg, "PNG", baos);
byte[] encodeBase64 = Base64.encodeBase64(baos.toByteArray());

String base64Encoded = new String(encodeBase64);
base64Encoded = "data:image/jpeg;base64,"+base64Encoded;
baos.close();

```

Adding `data:image/jpeg;base64,` is to show the image in the html. If you do not need this, just delete it.

## Trouble shooting

In the beginning, we thought iOS could capture a JPEG file, therefore we put `ImageIO.write(thumbImg, "PNG", baos);` as JPEG file format. But we saw the color for images were all wrong and it was pink. We could understand why it happened. After searched around, we found this [answer](https://stackoverflow.com/questions/26115851/pink-reddish-tint-while-resizing-jpeg-images-using-java-thumbnailator-or-imgscal). 
> The problem is almost always that you are reading/writing out different file formats and the ALPHA channel is causing one of your color channels (R/G/B) to be culled from the resulting file.
For example, if you read in a file that was ARGB (4 channel) and wrote it out as a JPG (3 channel) - unless you purposefully manipulate the image types yourself and render the old image to the new one directly, you will get a file with a "ARG" channels... or more specifically, just Red and Green - no Blue.
PNG supports an alpha channel and JPG does not, so be aware of that.

Therefore, when we changed file type to PNG, it finally showed the right image!