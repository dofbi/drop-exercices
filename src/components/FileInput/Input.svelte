<script>
  import { PutObjectCommand, S3Client } from '@aws-sdk/client-s3';

	let files;
  let isupload = false;
  let className = '';
  let studentName = '';
  let questions = [
		{ id: 1, text: `CI` },
		{ id: 2, text: `CP` },
		{ id: 3, text: `CE1` },
	];

  const CREDENTIAL = {
    accessKeyId: import.meta.env.PUBLIC_ACCESS_KEY_ID,
    secretAccessKey: import.meta.env.PUBLIC_SECRET_ACCESS_KEY,
  };

	$: if (files) {

    const client = new S3Client({
      credentials: CREDENTIAL,
      region: import.meta.env.PUBLIC_REGION
    });

		for (const file of files) {

      if (!files || !className || !studentName) {
			  alert(`Veuillez sélectionner des fichiers et remplir les noms de classe et d'étudiant.`);
        
		  }

      const filePath = `${className}/${studentName}/${file.name}`;

      const command = new PutObjectCommand({
        Bucket: import.meta.env.PUBLIC_BUCKET_NAME,
        Key: filePath,
        Body: file,
      });

      try {
        const response = client.send(command);
        console.log(response);
        if (response) isupload = true;

      } catch (err) {
        console.error(err);
      }
		}
	}
</script>

<p>
  <label for="classe-name">Nom Classe:</label>
  <select id="classe-name" bind:value={className}>
		{#each questions as question}
			<option value={question.text}>
				{question.text}
			</option>
		{/each}
	</select>
</p>

<p>
  <label for="student-name">Nom élève:</label>
  <input type="text" id="student-name" bind:value={studentName} />
</p>

<p>
  <input
	bind:files
	id="many"
	multiple
	type="file"
  />
</p>

{#if isupload}
	<h2>Fichiers sélectionnés:</h2>
	{#each Array.from(files) as file}
		<p>{file.name} ({file.size} bytes) </p>
	{/each}
{/if}
