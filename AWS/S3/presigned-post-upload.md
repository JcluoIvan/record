

## Example 
```typescript
import express from "express";
import AWS from "aws-sdk";
const PORT = 3000;
const AWS_BUCKET = "{AWS Bucket}";
const AWS_ACCESS_KEY_ID = "{AWS access key ID}";
const AWS_SECRET_ACCESS_KEY = "{AWS secret access key}";
const AWS_REGION = "{AWS Region}";

AWS.config.update({
  credentials: new AWS.Credentials({
    accessKeyId: AWS_ACCESS_KEY_ID,
    secretAccessKey: AWS_SECRET_ACCESS_KEY,
  }),
  region: AWS_REGION,
});
const s3 = new AWS.S3({ apiVersion: "2006-03-01" });

const app = express();
app.get("/upload/demo", (req, res) => {
  const key = "upload/" + new Date().getTime();
  const { url, fields } = s3.createPresignedPost({
    Bucket: AWS_BUCKET,
    Expires: 600,
    Fields: { key },
  });
  const html = `
    <html>
      <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
      </head>
      <body>
      <form action="${url}" method="post" enctype="multipart/form-data">
        Key to upload: 
        <input type="input"  name="key" value="${fields.key}" /><br />

        <input type="text"   name="X-Amz-Credential" value="${fields["X-Amz-Credential"]}" />
        <input type="text"   name="X-Amz-Algorithm" value="AWS4-HMAC-SHA256" />
        <input type="text"   name="X-Amz-Date" value="${fields["X-Amz-Date"]}" />

        Tags for File: 
        <input type="hidden" name="Policy" value="${fields.Policy}" />
        <input type="hidden" name="X-Amz-Signature" value="${fields["X-Amz-Signature"]}" />
        File: 
        <input type="file"   name="file" /> <br />
        <!-- The elements after this will be ignored -->
        <input type="submit" name="submit" value="Upload to Amazon S3" />
      </form>
      </body>
    </html>
  `;
  res.send(html);
});

app.listen(PORT, () => {
  console.info(`start server ${PORT}`);
});

```
