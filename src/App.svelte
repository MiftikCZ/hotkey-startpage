<script lang="ts">
  import { onMount } from "svelte";
  import "@fontsource/iosevka";
  import { Chslang } from "../node_modules/chslang/chslang";
  import KeyBind from "./lib/KeyBind.svelte";


  let show = "";
  let inputval = "";

  const chs = Chslang(
    {},
    {
      "action:elm": (cont, conf) => {
        if(show == "notes") inputval = ""
        show = "elm";
      },
      "action:ddg": (cont, conf) => {
        if(show == "notes") inputval = ""
        show = "ddg";
      },
      "action:google": (cont, conf) => {
        if(show == "notes") inputval = ""
        show = "google";
      },
      "action:notes": (cont, conf) => {
        show = "notes";
        inputval = localStorage.getItem("notes")
      },
      "action:aur": (cont, conf) => {
        show = "aur";
        inputval = ""
      },
      "action:open": (cont, conf) => {
        show = "open";
        inputval = ""
      },
      "action:close": (cont, conf) => {
        show = "";
        inputval = ""
      },
    }
  );
  let keys = [];
  function add_key(key, fun) {
    keys.push([key, fun]);
  }

  function wrap_fun(target: string) {
    return () => {
      chs.runline("action:" + target, "", {});
    };
  }

  let bels: Object = {};

  onMount(() => {
    window.addEventListener("keydown", (ev) => {
      if (!ev.repeat) {
        try {
          if (ev.ctrlKey && ev.key == "c") {
            chs.runline("action:close");
          } else {
            if (!show) {
              keys.find((e) => {
                return e[0] == ev.key.toLocaleLowerCase();
              })[1]();
            }
          }

          if (show) {
            Object.keys(bels).forEach((e) => {
              if (e != show) {
                bels[e].classList.remove("more_gray");
              } else {
                bels[e].classList.add("more_gray");
              }
            });
          } else {
            Object.keys(bels).forEach((e) => {
              bels[e].classList.remove("more_gray");
            });
          }
        } catch (error) {}
      }
    });
  });

  function inputadd(ev) {
    if (ev.key == "Enter") {
      if(show == "ddg") {
        window.open("https://duckduckgo.com/?q="+inputval)
      }
      if(show == "google") {
        window.open("https://google.com/search?q="+inputval)
      }
      if(show == "aur") {
        window.open("https://aur.archlinux.org/packages?O=0&SeB=nd&K="+encodeURIComponent(inputval)+"&outdated=&SB=p&SO=d&PP=50&submit=Go")
      }
      if(show == "elm") {
        window.open("https://edlinkme.miftik.tk/?r="+(btoa("/text/1.4/?q="+inputval)))
      }
      if(show == "open") {
        if(inputval.startsWith("http:")) inputval.replace(/http\:/gi, "")
        if(inputval.startsWith("http://")) inputval.replace(/http\:\/\//gi, "")
        if(inputval.startsWith("https:")) inputval.replace(/https\:/gi, "")
        if(inputval.startsWith("https://")) inputval.replace(/https\:\/\//gi, "")
        if(inputval.startsWith("://")) inputval.replace(/\:\/\//gi, "")
        window.open("https://"+inputval)
      }
      inputval = ""
    }
  }

  function inputarea() {
    localStorage.setItem("notes", inputval)
  }
</script>

<main>
  {#if show == "ddg"}
    <div class="elem input_container abs">
      <input type="text" bind:value={inputval} on:keypress={inputadd} class="input" autofocus/>
    </div>
  {/if}
  {#if show == "elm"}
    <div class="elem input_container abs">
      <input type="text" bind:value={inputval} on:keypress={inputadd} class="input" autofocus/>
    </div>
  {/if}
  {#if show == "aur"}
    <div class="elem input_container abs">
      <input type="text" bind:value={inputval} on:keypress={inputadd} class="input" autofocus/>
    </div>
  {/if}
  {#if show == "google"}
  <div class="elem input_container abs">
    <input type="text" bind:value={inputval} on:keypress={inputadd} class="input" autofocus/>
  </div>
  {/if}
  {#if show == "open"}
  <div class="elem input_container abs">
    <input type="text" bind:value={inputval} on:keypress={inputadd} class="input" autofocus/>
  </div>
  {/if}
  {#if show == "notes"}
  <div class="elem input_container abs">
    <textarea bind:value={inputval} on:input={inputarea} class="input" autofocus/>
  </div>
  {/if}
  <div class="elem grid">
    <KeyBind
      {add_key}
      bind:th={bels["elm"]}
      key="e"
      title="EDLinkMe"
      fun={wrap_fun("elm")}
    />
    <KeyBind
      {add_key}
      bind:th={bels["notes"]}
      key="n"
      title="Notes"
      fun={wrap_fun("notes")}
    />
    <KeyBind
      {add_key}
      bind:th={bels["open"]}
      key="q"
      title="Open Link"
      fun={wrap_fun("open")}
    />
    <KeyBind
      {add_key}
      bind:th={bels["ddg"]}
      key="d"
      title="Duckduckgo"
      fun={wrap_fun("ddg")}
    />
    <KeyBind
      {add_key}
      bind:th={bels["google"]}
      key="w"
      title="Google"
      fun={wrap_fun("google")}
    />
    <KeyBind
      {add_key}
      bind:th={bels["aur"]}
      key="a"
      title="AUR"
      fun={wrap_fun("aur")}
    />
    <KeyBind
      {add_key}
      bind:th={bels["close"]}
      key="ctrl+c"
      key_title="Ctrl+c"
      title="Close"
      fun={wrap_fun("close")}
    />
  </div>
  <div class="elem">
    selected [{show}]
  </div>
</main>

<style>
  :root {
    --bg: #24273a;
    --bg-second: #1e2030;
    --bg-third: #6e738d;
    --fg: #cad3f5;

    --elem-width: 600px;
  }

  main {
    font-family: "iosevka", monospace;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    height: 100vh;
    padding: 0.25em;
    background: var(--bg);
    color: var(--fg);
    gap: 0.75em;
  }

  .abs {
    position: absolute;
  }

  .input_container {
    padding: 1em !important;
    border: var(--bg-third) 1px solid;
    box-shadow: var(--bg-third) 4px 4px 4px !important;
  }

  .input_container input {
    border: #0001 1px solid;
    border-radius: 0.25em;
    padding: 0.25em;
    background: #6e738d;
    color: #cad3f5;
    font-size: 1em;
  }

  .input_container textarea {
    width: 400px;
    height: 200px;
    border: #0001 1px solid;
    border-radius: 0.25em;
    padding: 0.25em;
    background: #6e738d;
    color: #cad3f5;
    font-size: .75em;
  }

  .elem {
    max-width: var(--elem-width);
    background: var(--bg-second);
    box-shadow: var(--bg-second) 4px 4px 4px;
    padding: 0.25em;
  }

  .grid {
    column-gap: 1em;
    color: #b8c0e0;
    display: grid;
    grid-template-columns: repeat(3, 1fr);
  }

  :global(.real_gray) {
    color: #a5adcb;
  }

  :global(.more_gray) {
    color: #ee99a0;
  }
</style>
