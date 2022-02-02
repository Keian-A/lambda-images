# lambda-images

Function:

```js

const aws = require('aws-sdk');
const s3 = new aws.S3({ apiVersion: '2006-03-01' });

exports.handler = async (event) => {
    
    try {
        console.log('Event', JSON.stringify(event));
        
        // Bucket name
        const triggerBucket = event.Records[0].s3.bucket.name;
        
        // New key added (AKA FILE PATH)
        const newKey = event.Records[0].s3.object.key;
            
        // Get existing data
        let existing = await s3.getObject({
            Bucket: triggerBucket,
            Key: 'images.JSON'
        }).promise();
        
        // Parse JSON object
        let existingData = JSON.parse(existing.Body);
        
        // Get folder name
        const imageName = newKey.subString(newKey.indexOf('/'));
        
        // Check if we have an array.
        if (!Array.isArray(existingData)) {
            // Create array.
            existingData = [];
        }
        
        existingData.push(imageName);
        
        await s3.putObject({
            Bucket: triggerBucket,
            Key: 'images.JSON',
            Body: JSON.stringify(existingData),
            ContentType: 'application/json'
        }).promise();
        
        console.log(`Added new folder name ${imageName}`);
        
        return imageName;
    } catch (err) {
        return err;
    }
};


```

[JSON Data](https://keian-first-bucket.s3.us-west-2.amazonaws.com/images.json)
