<script>
    let { children } = $props();
    import "../app.css";
    import Titlebar from "custom-tauri-titlebar";
    import { getCurrentWindow } from "@tauri-apps/api/window";
    import { openUrl } from "@tauri-apps/plugin-opener";

    const titlebar = new Titlebar({
        theme: {
            bgPrimary: "#2c3e50",
            bgSecondary: "#34495e",
            fontPrimary: "#ecf0f1",
            fontSecondary: "#bdc3c7",
        },
    });
    titlebar.addTitle("Steam Screenshot Downloader");
    titlebar.addIcon({
        type: "src",
        data: "./logo.svg",
    });
    titlebar.addMenu({
        label: "File",
        items: [
            {
                label: "Help",
                type: "item",
                action: async () => {
                    await openUrl(
                        "https://github.com/Quantum-Pi/steam-screenshot-downloader",
                    );
                },
            },
        ],
    });
    titlebar.addButton(
        "btn-close",
        { type: "html", data: "<p>X</p>" },
        async () => {
            await getCurrentWindow().close();
        },
    );
</script>

<div class="mt-4">
    {@render children()}
</div>
