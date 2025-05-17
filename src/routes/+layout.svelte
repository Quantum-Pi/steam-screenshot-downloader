<script>
    let { children } = $props();
    import "../app.css";
    import Titlebar from "custom-tauri-titlebar";
    import { getCurrentWindow } from "@tauri-apps/api/window";

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
        data: "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyNCIgaGVpZ2h0PSIyNCIgdmlld0JveD0iMCAwIDI0IDI0Ij48cGF0aCBmaWxsPSJjdXJyZW50Q29sb3IiIGQ9Ik01IDIxcS0uODI1IDAtMS40MTItLjU4N1QzIDE5VjVxMC0uODI1LjU4OC0xLjQxMlQ1IDNoMTRxLjgyNSAwIDEuNDEzLjU4OFQyMSA1djE0cTAgLjgyNS0uNTg3IDEuNDEzVDE5IDIxem0xLTguNDI1bDQtNGw0IDRsNC00bDEgMVY1SDV2Ni41NzV6TTUgMTloMTR2LTYuNmwtMS0xbC00IDRsLTQtNGwtNCA0bC0xLTF6bTAgMHYtNi42djJWNXoiLz48L3N2Zz4=",
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
