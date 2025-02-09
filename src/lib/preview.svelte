<script lang="ts">
  import hotkeys from "hotkeys-js";

  import Banknotes from "../icon/banknotes.svelte";
  import Time from "../icon/time.svelte";
  import { loc, shop, type CafeShop } from "../store";
  import { getDistanceFromMe } from "../utils";

  let shopValue: CafeShop | null = null;
  shop.subscribe((value) => (shopValue = value));

  let myLoc: [number, number] = null;
  loc.subscribe((v) => (myLoc = v));

  $: image = shopValue?.properties["image"];
  $: name = shopValue?.properties["name"];
  $: address = shopValue?.properties["address"];
  $: open_time = shopValue?.properties["open_time"];
  $: proposed_price = shopValue?.properties["proposed_price"];

  $: myLocAsStr = myLoc?.slice().reverse().join(",");
  $: shopLocAsStr = shopValue?.coordinates.slice().reverse().join(",");
  $: mapLink = `https://www.google.com/maps/dir/?api=1&origin=${myLocAsStr}&destination=${shopLocAsStr}`;
  $: distance = getDistanceFromMe(shopValue?.coordinates || [0, 0], myLoc);

  const clearShop = () => shop.set(null);
  hotkeys("esc", clearShop);
</script>

{#if shopValue !== null}
  <div class="container">
    <button class="close" on:click={clearShop}><Time size={12} /></button>
    <div class="cover">
      <img src={image} alt={name} />
    </div>
    <div class="info">
      <div class="name">{name}</div>
      <div class="small address">
        {address}
        {#if distance}
          <span class="distance">
            {`・${distance} `}
            <a class="link" href={mapLink} target="_blank">Direction</a>
          </span>
        {/if}
      </div>
      <div class="small">{open_time}</div>
      <div class="small price">
        <Banknotes />
        {proposed_price}
      </div>
    </div>
  </div>
{/if}

<style scoped>
  .container {
    display: flex;
    gap: 12px;
    padding: 12px;
    position: absolute;
    bottom: 48px;
    left: 50%;
    transform: translateX(-50%);
    border-radius: 12px;
    background-color: var(--color-neutral-50);
    border: 1px solid var(--color-neutral-300);
    box-shadow: var(--shadow-5);
    max-width: calc(100vw - 48px);
  }
  .close {
    position: absolute;
    top: 4px;
    right: 4px;
    padding: 2px;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: transparent;
    color: var(--color-neutral-500);
    border: 0 none;
    cursor: pointer;
    border-radius: 50%;
  }
  .close:hover {
    background-color: var(--color-neutral-100);
  }
  .cover {
    width: 72px;
    height: 72px;
    border-radius: 6px;
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: hidden;
  }
  .cover img {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }
  .name {
    color: var(--color-neutral-600);
    font-weight: 600;
  }
  .small {
    color: var(--color-neutral-500);
    font-size: 12px;
    line-height: 16px;
  }
  .address {
    max-inline-size: 256px;
  }
  .price {
    margin-top: 8px;
    display: flex;
    align-items: center;
    gap: 2px;
  }
  .distance {
    font-weight: 500;
  }
  .link {
    text-decoration: none;
    color: var(--color-indigo-500);
  }

  @media (max-width: 641px) {
    .container {
      width: calc(100vw - 48px);
      bottom: 24px;
    }
    .address {
      max-inline-size: unset;
    }
  }
</style>
