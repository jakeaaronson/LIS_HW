<script>
  import { onMount } from 'svelte';
  import { navigate } from 'svelte-routing';
  
  let name = '';
  let age = '';
  let title = '';
  let hometown = '';
  
  let errors = {
    name: false,
    title: false
  };
  
  let entries = [];
  
  onMount(() => {
    const storedEntries = localStorage.getItem('entries');
    if (storedEntries) {
      entries = JSON.parse(storedEntries);
    }
  });
  
  function validateForm() {
    let isValid = true;
    
    errors = {
      name: false,
      title: false
    };
    
    if (!name.trim()) {
      errors.name = true;
      isValid = false;
    }
    
    if (!title.trim()) {
      errors.title = true;
      isValid = false;
    }
    
    return isValid;
  }
  
  function handleSubmit() {
    if (validateForm()) {
      const newEntry = {
        id: Date.now(),
        name,
        age,
        title,
        hometown
      };
      
      entries = [...entries, newEntry];
      
      localStorage.setItem('entries', JSON.stringify(entries));
      
      name = '';
      age = '';
      title = '';
      hometown = '';
      
      showConfirmation = true;
    }
  }
  
  let showConfirmation = false;
  
  function goBackToForm() {
    showConfirmation = false;
  }
</script>

<main>
  <div class="container">
    {#if !showConfirmation}
      <h1>Data Entry Form</h1>
      <form on:submit|preventDefault={handleSubmit}>
        <div class="form-group">
          <label for="name">Name *</label>
          <input 
            type="text" 
            id="name" 
            bind:value={name} 
            class:error={errors.name}
          />
          {#if errors.name}
            <span class="error-message">Name is required</span>
          {/if}
        </div>
        
        <div class="form-group">
          <label for="age">Age</label>
          <input 
            type="number" 
            id="age" 
            bind:value={age} 
            min="0"
          />
        </div>
        
        <div class="form-group">
          <label for="title">Title *</label>
          <input 
            type="text" 
            id="title" 
            bind:value={title} 
            class:error={errors.title}
          />
          {#if errors.title}
            <span class="error-message">Title is required</span>
          {/if}
        </div>
        
        <div class="form-group">
          <label for="hometown">Hometown</label>
          <input 
            type="text" 
            id="hometown" 
            bind:value={hometown}
          />
        </div>
        
        <button type="submit" class="submit-btn">Submit</button>
      </form>
    {:else}
      <div class="confirmation">
        <h1>Confirmation</h1>
        <div class="entry-details">
          <h2>Entry Details:</h2>
          <p><strong>Name:</strong> {entries[entries.length - 1].name}</p>
          <p><strong>Age:</strong> {entries[entries.length - 1].age || 'Not provided'}</p>
          <p><strong>Title:</strong> {entries[entries.length - 1].title}</p>
          <p><strong>Hometown:</strong> {entries[entries.length - 1].hometown || 'Not provided'}</p>
        </div>
        
        <h2>Previous Entries</h2>
        {#if entries.length > 0}
          <table>
            <thead>
              <tr>
                <th>Name</th>
                <th>Age</th>
                <th>Title</th>
                <th>Hometown</th>
              </tr>
            </thead>
            <tbody>
              {#each entries as entry}
                <tr>
                  <td>{entry.name}</td>
                  <td>{entry.age || 'N/A'}</td>
                  <td>{entry.title}</td>
                  <td>{entry.hometown || 'N/A'}</td>
                </tr>
              {/each}
            </tbody>
          </table>
        {:else}
          <p>No previous entries</p>
        {/if}
        
        <button class="back-btn" on:click={goBackToForm}>Back to Form</button>
      </div>
    {/if}
  </div>
</main>

<style>
  .container {
    max-width: 800px;
    margin: 0 auto;
    padding: 20px;
    font-family: Arial, sans-serif;
  }
  
  h1 {
    color: #333;
    text-align: center;
    margin-bottom: 30px;
  }
  
  .form-group {
    margin-bottom: 20px;
  }
  
  label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
  }
  
  input {
    width: 100%;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 16px;
  }
  
  input.error {
    border-color: #ff3e00;
  }
  
  .error-message {
    color: #ff3e00;
    font-size: 14px;
    margin-top: 5px;
    display: block;
  }
  
  .submit-btn, .back-btn {
    background-color: #4CAF50;
    color: white;
    padding: 12px 20px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 16px;
    display: block;
    margin: 20px 0;
    width: 100%;
  }
  
  .submit-btn:hover, .back-btn:hover {
    background-color: #45a049;
  }
  
  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
  }
  
  th, td {
    border: 1px solid #ddd;
    padding: 10px;
    text-align: left;
  }
  
  th {
    background-color: #f2f2f2;
    font-weight: bold;
  }
  
  tr:nth-child(even) {
    background-color: #f9f9f9;
  }
  
  .confirmation {
    background-color: #f9f9f9;
    padding: 20px;
    border-radius: 4px;
    border: 1px solid #ddd;
  }
  
  .entry-details {
    background-color: white;
    padding: 15px;
    border-radius: 4px;
    margin-bottom: 20px;
    border: 1px solid #eee;
  }
</style>