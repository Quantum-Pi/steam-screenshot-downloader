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
        data: "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyNCIgaGVpZ2h0PSIyNCIgdmlld0JveD0iMCAwIDI0IDI0Ij48cGF0aCBmaWxsPSIjZmZmIiBkPSJNNSAyMXEtLjgyNSAwLTEuNDEyLS41ODdUMyAxOVY1cTAtLjgyNS41ODgtMS40MTJUNSAzaDE0cS44MjUgMCAxLjQxMy41ODhUMjEgNXYxNHEwIC44MjUtLjU4NyAxLjQxM1QxOSAyMXptMS04LjQyNWw0LTRsNCA0bDQtNGwxIDFWNUg1djYuNTc1ek01IDE5aDE0di02LjZsLTEtMWwtNCA0bC00LTRsLTQgNGwtMS0xem0wIDB2LTYuNnYyVjV6Ii8+PC9zdmc+",
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
