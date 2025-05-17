<script lang="ts">
  import { ExternalLink } from "@lucide/svelte";
  import { fetch } from "@tauri-apps/plugin-http";
  import { open } from "@tauri-apps/plugin-dialog";
  import { download } from "@tauri-apps/plugin-upload";

  let userMode: "STEAM_ID" | "USERNAME" = $state("USERNAME");

  let processedFiles = $state(0);
  let fileCount = $state(0);

  let log: string[] = $state([]);
  let logBody = $derived(
    log.join("\n").replace("{PROCESS_COUNT}", `${processedFiles}/${fileCount}`),
  );

  let apiKey = $state("");
  let userValue = $state("");
  let appId = $state("");
  let saveDir: string | null = $state(null);

  let running = $state(false);

  function pushLog(message: string) {
    log = [...log, message];
  }

  async function getSteamId() {
    if (userMode === "STEAM_ID") {
      return userValue;
    }
    const resp = await fetchSteam(
      `https://api.steampowered.com/ISteamUser/ResolveVanityURL/v1/?key=${apiKey}&vanityurl=${userValue}`,
      "Error getting SteamID from Username",
    );
    if (!resp) {
      return null;
    }
    const json = await resp.json();
    return json.response.steamid;
  }

  const handleError = (condition: boolean, msg: string) => {
    if (condition) {
      console.error(msg);
      pushLog(msg);
    }
    return condition;
  };

  const fetchSteam = async (url: string, errorMsg: string) => {
    const resp = await fetch(url, {
      method: "GET",
      headers: {
        "Content-Type": "application/json",
      },
    });
    if (resp.status !== 200) {
      console.error(`${errorMsg} (${resp.status}: ${resp.statusText})`);
      pushLog(`${errorMsg} (${resp.status}: ${resp.statusText})`);
      running = false;
      return null;
    }
    return resp;
  };

  async function handleSubmit(event: Event) {
    event.preventDefault();

    running = true;
    log = [];

    if (handleError(!saveDir, "No directory selected")) {
      running = false;
      return;
    }

    const steamId = await getSteamId();
    if (handleError(!steamId, "Invalid Steam ID")) {
      running = false;
      return;
    }

    if (userMode === "STEAM_ID") {
      pushLog(`Steam ID for ${userValue}: ${steamId}`);
    }

    let resp = await fetchSteam(
      `https://api.steampowered.com/IPublishedFileService/GetUserFiles/v1/?key=${apiKey}&steamid=${steamId}&appid=${appId}&filetype=4`,
      "Error fetching data",
    );
    if (!resp) {
      return;
    }

    const total = (await resp.json()).response.total;

    resp = await fetchSteam(
      `https://api.steampowered.com/IPublishedFileService/GetUserFiles/v1/?key=${apiKey}&steamid=${steamId}&appid=${appId}&filetype=4&numperpage=${total}`,
      "Error fetching data",
    );
    if (!resp) {
      return;
    }

    pushLog(`Downloading ${total} files...`);

    const files: { file_url: string }[] = (await resp.json()).response
      .publishedfiledetails;
    fileCount = files.length;

    pushLog(`{PROCESS_COUNT}`);

    const urls = files.map((file, i) =>
      download(file.file_url, `${saveDir}/${appId}_${i}.jpg`)
        .then(() => {
          processedFiles = processedFiles + 1;
        })
        .catch((err) => {
          console.error(`Error downloading file ${i + 1}`, err);
          pushLog(`Error downloading file ${i + 1} (${err})`);
        }),
    );
    await Promise.all(urls);

    pushLog("Download complete!");
    running = false;
  }
</script>

<main class="w-screen h-screen">
  <div class="flex flex-col items-center justify-center h-full gap-y-8">
    <form
      class="mx-auto mt-8 w-full max-w-md space-y-4"
      onsubmit={handleSubmit}
    >
      <label class="label">
        <span class="label-text">Web API Key</span>
        <div class="input-group grid-cols-[1fr_auto]">
          <input
            type="password"
            placeholder="Steam Web API Key"
            class="ig-input"
            bind:value={apiKey}
          />
          <a
            class="ig-btn preset-filled"
            title="Get API Key"
            href="https://steamcommunity.com/dev/apikey"
            target="_blank"
          >
            <ExternalLink />
          </a>
        </div>
      </label>
      <label class="label">
        <span class="label-text">User</span>
        <div class="input-group grid-cols-[auto_1fr_auto]">
          <select class="ig-select" bind:value={userMode}>
            <option value="STEAM_ID">SteamID</option>
            <option value="USERNAME">Username</option>
          </select>
          <input
            type={userMode === "STEAM_ID" ? "number" : "text"}
            class="ig-input"
            placeholder={userMode === "STEAM_ID" ? "Steam ID" : "Username"}
            bind:value={userValue}
          />
          <!-- {#if userMode === "STEAM_ID"}
            <button
              class="ig-btn preset-filled"
              title="Get Steam ID"
            >
              <ExternalLink />
            </button>
          {/if} -->
        </div>
      </label>
      <label class="label">
        <span class="label-text">AppID</span>
        <input
          type="number"
          class="input"
          placeholder="Game App ID"
          bind:value={appId}
        />
      </label>
      <label class="label">
        <button
          class="btn preset-filled"
          type="button"
          onclick={() => {
            open({
              directory: true,
              multiple: false,
            }).then((dir) => {
              if (dir) {
                saveDir = dir;
                console.log(dir);
              }
            });
          }}>{saveDir ? saveDir : "Choose Directory"}</button
        >
        <span>{saveDir ? "" : "No directory selected"}</span>
      </label>
      <button
        type="submit"
        class="btn preset-filled-primary-500"
        disabled={running}>Submit</button
      >
    </form>
    <textarea
      class="textarea w-3/4 h-[90%] mb-8"
      rows="5"
      disabled
      value={logBody}
    ></textarea>
  </div>
</main>
