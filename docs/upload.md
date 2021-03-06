# Uploading Images

## Server / API

You can get the original image ByteData and pass it to the http request like this:

```dart
// string to uri
Uri uri = Uri.parse('$_apiEndpoint/some/path');

// create multipart request
MultipartRequest request = http.MultipartRequest("POST", uri);

ByteData byteData = await asset.requestOriginal();
List<int> imageData = byteData.buffer.asUint8List();

MultipartFile multipartFile = MultipartFile.fromBytes(
  'photo',
  imageData,
  filename: 'some-file-name.jpg',
  contentType: MediaType("image", "jpg"),
);

// add file to multipart
request.files.add(multipartFile);
// send
var response = await request.send();

...

// Don't forget to release the image data after you no longer need it
asset.releaseOriginal();
```

## Firebase

You can pass the image data directly, like this:

```dart
Future saveImage(Asset asset) async {
  ByteData byteData = await asset.requestOriginal();
  List<int> imageData = byteData.buffer.asUint8List();
  StorageReference ref = FirebaseStorage.instance.ref().child("some_image_bame.jpg");
  StorageUploadTask uploadTask = ref.putData(imageData);

  // Release the image data
  asset.releaseOriginal();

  return await (await uploadTask.onComplete).ref.getDownloadURL();
}
```
