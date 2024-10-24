<script>
  import { onMount } from "svelte";
  let file = null;
  let prompt = "";
  let openAiResponse = "";
  let artData = null;

  const handleFileChange = (event) => {
    file = event.target.files[0];
  };

  const handleFileUpload = async () => {
    if (file) {
      const formData = new FormData();
      formData.append("file", file);

      try {
        const response = await fetch("/api/upload", {
          method: "POST",
          body: formData,
        });
        const result = await response.json();
        alert(`File uploaded: ${result.filePath}`);
      } catch (error) {
        console.error("Error uploading file:", error);
      }
    }
  };

  const handleOpenAiPrompt = async () => {
    try {
      const response = await fetch("/api/openai-prompt", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ prompt }),
      });
      const result = await response.json();
      openAiResponse = result;
    } catch (error) {
      console.error("Error with OpenAI:", error);
    }
  };

  const fetchSmithsonianData = async () => {
    try {
      const response = await fetch("/api/smithsonian-collections");
      const result = await response.json();
      artData = result;
    } catch (error) {
      console.error("Error fetching Smithsonian data:", error);
    }
  };
</script>

<main>
  <h1 class="text-2xl font-bold mb-4">Tattoo Assistant (Agent Zero)</h1>

  <!-- File Upload Section -->
  <div class="mb-6">
    <h2 class="text-xl mb-2">Upload a File:</h2>
    <input type="file" on:change={handleFileChange} class="border p-2 mb-4" />
    <button on:click={handleFileUpload} class="bg-blue-500 text-white px-4 py-2">
      Upload File
    </button>
  </div>

  <!-- OpenAI Prompt Section -->
  <div class="mb-6">
    <h2 class="text-xl mb-2">Ask OpenAI:</h2>
    <textarea
      bind:value={prompt}
      class="border p-2 mb-4 w-full"
      rows="3"
      placeholder="Enter a prompt"
    ></textarea>
    <button on:click={handleOpenAiPrompt} class="bg-green-500 text-white px-4 py-2">
      Ask AI
    </button>

    {#if openAiResponse}
      <div class="mt-4 p-4 bg-gray-100 border">
        <strong>OpenAI Response:</strong>
        <p>{openAiResponse}</p>
      </div>
    {/if}
  </div>

  <!-- Smithsonian Data Section -->
  <div class="mb-6">
    <h2 class="text-xl mb-2">Smithsonian Collections:</h2>
    <button on:click={fetchSmithsonianData} class="bg-yellow-500 text-white px-4 py-2">
      Fetch Collections
    </button>

    {#if artData}
      <div class="mt-4 p-4 bg-gray-100 border">
        <strong>Collection Data:</strong>
        <pre>{JSON.stringify(artData, null, 2)}</pre>
      </div>
    {/if}
  </div>
</main>

<style>
  body {
    font-family: Arial, sans-serif;
  }

  button {
    cursor: pointer;
  }

  textarea {
    resize: vertical;
  }

  input[type="file"] {
    cursor: pointer;
  }
</style>