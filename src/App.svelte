<script lang="ts">
  // localStorage.clear();

  const defaultWelcomeMessage = "~$hello!";
  const defaultCategories = [
    {
      name: "Dev",
      items: [
        {
          name: "github",
          url: "https://github.com/",
        },
        {
          name: "boot.dev",
          url: "https://www.boot.dev/dashboard",
        },
      ],
    },
    {
      name: "Study",
      items: [
        {
          name: "canvas",
          url: "https://tacomacc.instructure.com/",
        },
        {
          name: "ctclink",
          url: "https://csprd.ctclink.us",
        },
      ],
    },
    {
      name: "Other",
      items: [
        {
          name: "youtube",
          url: "https://www.youtube.com/",
        },
      ],
    },
  ];
  // clean start
  if (
    !localStorage.getItem("welcomeMessage") ||
    localStorage.getItem("welcomeMessage") === ""
  ) {
    localStorage.setItem("welcomeMessage", defaultWelcomeMessage);
  }
  if (
    !localStorage.getItem("categories") ||
    localStorage.getItem("categories") === ""
  ) {
    localStorage.setItem("categories", JSON.stringify(defaultCategories));
  }

  type Link = {
    name: string;
    url: string;
  };
  type Category = {
    name: string;
    items: Link[];
  };

  let categories: Category[] = $state(
    JSON.parse(localStorage.getItem("categories") as string),
  );

  let welcomeMessage: string = $state(
    localStorage.getItem("welcomeMessage") as string,
  );

  let linkCount = $derived(
    categories.reduce((total, current) => {
      return total + current.items.length;
    }, 0),
  );

  $effect(() => {
    localStorage.setItem("welcomeMessage", welcomeMessage);
    localStorage.setItem("categories", JSON.stringify(categories));
  });

  let isEditMode = $state(false);
  function toggleEditMode() {
    isEditMode = !isEditMode;
  }

  function addLink(category: Category) {
    category.items.push({
      name: "link" + (linkCount + 1),
      url: "https://example.com/",
    });
  }
  function removeLink(category: Category, link: Link) {
    category.items = category.items.filter((item) => item.name !== link.name);
  }

  function addCategory() {
    categories.push({
      name: "Category" + (categories.length + 1),
      items: [
        {
          name: "link" + linkCount,
          url: "https://example.com/",
        },
      ],
    });
  }
  function deleteCategory(category: Category) {
    categories = categories.filter((item) => item.name !== category.name);
  }
</script>

<!-- Welcome Message -->
<div class="p-5 py-15 md:py-25 bg-green-800 flex justify-center text-5xl md:text-6xl lg:text-8xl font-extrabold">
  {#if isEditMode}
    <input class="w-80 md:w-100 lg:w-150" bind:value={welcomeMessage} />
  {:else}
    <h1>{welcomeMessage}</h1>
  {/if}
</div>

<div class="grid grid-cols-2 gap-6 md:grid-cols-3 lg:grid-cols-3 p-10 px-10 md:px-30 lg:px-40">


  <!-- Categories -->
  {#each categories as category, i}

    <div class="bg-blue-500 min-h-25 min-w-25">
      
      <!-- Category Title -->
      <div class="text-xl font-bold py-2">
        {#if isEditMode}
          <input bind:value={category.name} />
          <button onclick={() => deleteCategory(category)}>X</button>
        {:else}
          <h2>~/{category.name}</h2>
        {/if}
      </div>

      <!-- Links -->
      <ul>
        {#each category.items as link}
          <li>
            {#if isEditMode}
              <input bind:value={link.name} /><input
                bind:value={link.url}
              /><button onclick={() => removeLink(category, link)}
                >X</button
              >
            {:else}
              <a href={link.url}>{link.name}</a>
            {/if}
          </li>
        {/each}

        <!-- Add Link Button -->
        {#if isEditMode}
          <li>
            <button onclick={() => addLink(category)}>add new link</button>
          </li>
        {/if}
      </ul>
    </div>
  {/each}

  <!-- Add Category Button -->
  {#if isEditMode}
    <button onclick={addCategory}>Add Category</button>
  {/if}


</div>
<button onclick={toggleEditMode}>toggle</button>

<!-- {#if isEditMode}
    {#each categories as category, i}
      <div class="bg-blue-500 min-h-25">
        <input bind:value={category.name} />
        <button onclick={() => deleteCategory(category)}>Delete Category</button
        >
        <ul>
          {#each category.items as link}
            <li>
              <input bind:value={link.name} /><input
                bind:value={link.url}
              /><button onclick={() => removeLink(category, link)}
                >delete link</button
              >
            </li>
          {/each}
          <li>
            <button onclick={() => addLink(category)}>add new link</button>
          </li>
        </ul>

      </div>
    {/each}
    <button onclick={addCategory}>Add Category</button>
  {:else}
    {#each categories as category, i}
      <div class="bg-red-500 min-h-25 min-w-25">
        <h2>{category.name}</h2>
        <ul>
          {#each category.items as link}
            <li><a href={link.url}>{link.name}</a></li>
          {/each}
        </ul>
      </div>
    {/each}
  {/if} -->
