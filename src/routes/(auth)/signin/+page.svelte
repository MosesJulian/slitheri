<script lang="ts">
  let formData = {
    email: '',
    password: '',
  };

  const handleSubmit = async (e: Event) => {
    e.preventDefault();

    const res = await fetch('/api/auth/signup', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(formData),
    });

    const data = await res.json();

    if (data.error) {
      alert(data.error);
    } else {
      alert('Signed In successfully!');
      window.location.href = '/';
    }
  };
</script>

<main class="grid place-items-center min-h-screen bg-slate-100">
  <div class="flex flex-col p-5 rounded-md shadow-md gap-4 max-w-[500px] w-full bg-white">
    <h1 class="text-xl font-semibold">Sign In to Slitheri</h1>

    <form on:submit={handleSubmit} class="flex flex-col gap-4">
      <div class="flex flex-col justify-center">
        <label for="email">Email</label>
        <input name="email" type="email" bind:value={formData.email} placeholder="example@gmail.com" class="transition-all outline-none border duration-300 rounded-md hover:ring-1 hover:ring-green-500 hover:ring-offset-2 px-3 py-2" required />
      </div>

      <div class="flex flex-col justify-center">
        <label for="password">Password</label>
        <input name="password" type="password" bind:value={formData.password} placeholder="******" class="transition-all outline-none border duration-300 rounded-md hover:ring-1 hover:ring-green-500 hover:ring-offset-2 px-3 py-2" required />
      </div>
      
      <div class="flex items-center justify-center">
        <button type="submit" class="bg-green-600 text-white px-4 py-2 transition-colors duration-300 hover:bg-green-800 w-full rounded-md">
          Sign Up
        </button>
      </div>
    </form>

    <div>Don't have an account? <a href="/signup" class="text-blue-700">Sign Up</a></div>
  </div>
</main>
