<script>
import { daoActor, principal } from "../stores";
import { get } from "svelte/store";
import oldMan from "../assets/oldmanicp.gif";

let proposalContent;
let proposalTitle;
let summary;
let promise;

async function create_proposal(title, content) {
  let dao = get(daoActor);
  console.log(dao);
  if (!dao) {
    return;
  }
  let res = await dao.submit_proposal(title, content);
  if (res.ok) {
    return res.ok;
  } else {
    throw new Error(res.Err);
  }
}

function handleCreateClick(title, content) {
  promise = create_proposal(title, content);
}
</script>

<div class="flex gap-x-4">
  <div class="flex items-center justify-center">
    <img class="w-80" src="{oldMan}" alt="icplogo" />
  </div>
  <div class="flex flex-wrap items-center">
    {#if $principal}
      <h1 class="w-full text-3xl font-bold">New Proposal</h1>

      {#await promise}
        <p class="w-full">...waiting</p>
      {:then proposal}
        {#if proposal}
          <p class="w-full">
            {proposal}
          </p>
        {/if}
      {:catch error}
        <p class="w-full" style="color: red">{error.message}</p>
      {/await}

      <div class="flex w-full flex-wrap justify-center gap-y-4">
        <input
          bind:value="{proposalTitle}"
          class="w-full border border-black px-2 py-2"
          placeholder="Proposal title" />
        <input
          bind:value="{proposalContent}"
          class="w-full border border-black px-2 py-2"
          placeholder="Update text to..." />
        <button
          class="w-3/4 border bg-white px-4 py-2 text-black hover:border-none hover:bg-lime-400 hover:shadow-hard"
          on:click="{handleCreateClick(proposalTitle, proposalContent)}"
          >Create</button>
      </div>
    {:else}
      <p>Connect with a wallet to create a new proposal.</p>
    {/if}
  </div>
</div>
