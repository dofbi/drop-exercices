<script>
  import { S3Client, ListObjectsCommand } from '@aws-sdk/client-s3';

  const CREDENTIAL = {
    accessKeyId: import.meta.env.PUBLIC_ACCESS_KEY_ID,
    secretAccessKey: import.meta.env.PUBLIC_SECRET_ACCESS_KEY,
  };
  
  const client = new S3Client({
    credentials: CREDENTIAL,
    region: import.meta.env.PUBLIC_REGION
  });
  
  let files = [];
  
  async function getFiles() {
    try {
      const input = {
        Bucket: import.meta.env.PUBLIC_BUCKET_NAME,
      };
      const command = new ListObjectsCommand(input);
      const response = await client.send(command);
      files = response.Contents;
    } catch (error) {
      console.log(error);
    }
  }

  getFiles()

</script>

{#if files.length > 0}
<h2>Excercices {files.length}</h2>
<ul>
  {#each files as file}
    <li><a href={`https://${import.meta.env.PUBLIC_BUCKET_NAME}.s3.${import.meta.env.PUBLIC_REGION}.amazonaws.com/${file.Key}`}>{file.Key}</a></li>
  {/each}
</ul>
{/if}