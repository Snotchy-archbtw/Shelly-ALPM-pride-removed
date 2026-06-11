# Maintainer: Zoey Bauer <zoey.erin.bauer@gmail.com>
# Maintainer: Caroline Snyder <hirpeng@gmail.com>
pkgname=shelly-degayed
pkgver=2.3.2.3
pkgrel=1
pkgdesc="Shelly: A Modern Arch Package Manager"
arch=('x86_64')
url="https://github.com/Seafoam-Labs/Shelly-ALPM"
license=('GPL-3.0-only')
provides=('shelly')
conflicts=('shelly-git' 'shelly-bin')
depends=(
    'pacman'
    'gtk4'
    'glib2'
    'sudo'
    'tar'
    'bash'
    'git'
    'hicolor-icon-theme'
    'dbus'
    'glibc'
    'libarchive'
    'dconf'
    'gnupg'
    'zstd'
)
optdepends=(
    'flatpak: For supporting flatpak implementation.'
    'fish: Fish shell completions'
)
makedepends=('dotnet-sdk-10.0' 'clang' 'gettext')

# Source tarball from GitHub release
source=("https://github.com/Snotchy-archbtw/Shelly-ALPM-pride-removed/archive/refs/tags/v${pkgver}.tar.gz")

sha256sums=('f55cf16b2a3cf63ef07566c005d7783a0fe3618c19798f8bf4b2a5bb1256618e')

build() {
  cd "$srcdir/Shelly-ALPM-pride-removed-${pkgver}"

  dotnet publish Shelly-CLI/Shelly-CLI.csproj -c Release -o out-cli --nologo -p:InstructionSet=${INSTRUCTIONS:=x86-64}
  dotnet publish Shelly.Gtk/Shelly.Gtk.csproj -c Release -r linux-x64 -o out --nologo -p:InstructionSet=${INSTRUCTIONS:=x86-64}
  dotnet publish Shelly-Notifications/Shelly-Notifications.csproj -c Release -r linux-x64 -o out-notify --nologo -p:InstructionSet=${INSTRUCTIONS:=x86-64}
  dotnet publish Shelly.Keys/Shelly.Keys.csproj -c Release -r linux-x64 -o out-keys --nologo -p:InstructionSet=${INSTRUCTIONS:=x86-64}

  # Compile translations
  for po_file in Shelly.Gtk/po/*.po; do
    if [ -f "$po_file" ]; then
      lang=$(basename "$po_file" .po)
      msgfmt "$po_file" -o "shelly-ui-${lang}.mo"
    fi
  done
  
  # Compile tray service translations
    for po_file in Shelly-Notifications/po/*.po; do
      if [ -f "$po_file" ]; then
        lang=$(basename "$po_file" .po)
        msgfmt "$po_file" -o "shelly-notifications-${lang}.mo"
      fi
    done
}

package() {
  cd "$srcdir/Shelly-ALPM-pride-removed-${pkgver}"
  # Install binaries
  [ -f "$pkgdir/usr/bin/shelly-ui" ] || install -Dm755 out/shelly-ui "$pkgdir/usr/bin/shelly-ui"
  [ -f "$pkgdir/usr/bin/shelly-notifications" ] || install -Dm755 out-notify/Shelly-Notifications "$pkgdir/usr/bin/shelly-notifications"
  [ -f "$pkgdir/usr/bin/shelly" ] || install -Dm755 out-cli/shelly "$pkgdir/usr/bin/shelly"
  [ -f "$pkgdir/usr/bin/shelly-keys" ] || install -Dm755 out-keys/shelly-keys "$pkgdir/usr/bin/shelly-keys"

  # Install desktop entries
  [ -f "$pkgdir/usr/share/applications/com.shellyorg.shelly.desktop" ] || cat <<'EOF' | install -Dm644 /dev/stdin "$pkgdir/usr/share/applications/com.shellyorg.shelly.desktop"
[Desktop Entry]
Name=Shelly
Comment=A Modern Arch Package Manager
Exec=/usr/bin/shelly-ui
Icon=shelly
Type=Application
Categories=System;Utility;
Keywords=program;software;store;repository;package;add;install;uninstall;remove;update;apps;applications;flatpak;pacman;aur;appimage;
Terminal=false
Actions=FlatpakInstall;FlatpakUpdate;FlatpakRemove;
[Desktop Action FlatpakInstall]
Name=Flatpak Install
Icon=flatpak-symbolic
Exec=/usr/bin/shelly-ui --page flatpak-install
[Desktop Action FlatpakUpdate]
Name=Flatpak Update
Icon=flatpak-symbolic
Exec=/usr/bin/shelly-ui --page flatpak-update
[Desktop Action FlatpakRemove]
Name=Flatpak Remove
Icon=flatpak-symbolic
Exec=/usr/bin/shelly-ui --page flatpak-remove
EOF

  [ -f "$pkgdir/usr/share/applications/com.shellyorg.shelly-notifications.desktop" ] || cat <<'EOF' | install -Dm644 /dev/stdin "$pkgdir/usr/share/applications/com.shellyorg.shelly-notifications.desktop"
[Desktop Entry]
Name=Shelly Notifications
Comment=Notification service for Shelly package manager
Exec=/usr/bin/shelly-notifications
Icon=shelly-tray
Type=Application
Categories=System;Utility;
Keywords=program;software;store;repository;package;add;install;uninstall;remove;update;apps;applications;flatpak;pacman;aur;appimage;
Terminal=false
NoDisplay=true
EOF

  # Install icons
  [ -f "$pkgdir/usr/share/icons/hicolor/256x256/apps/shelly.png" ] || install -Dm644 Shelly.Gtk/Assets/shellylogo.png "$pkgdir/usr/share/icons/hicolor/256x256/apps/shelly.png"
  [ -f "$pkgdir/usr/share/icons/hicolor/256x256/apps/shelly-tray.png" ] || install -Dm644 Shelly.Gtk/Assets/shellylogo-tray.png "$pkgdir/usr/share/icons/hicolor/256x256/apps/shelly-tray.png"
  [ -f "$pkgdir/usr/share/icons/hicolor/256x256/apps/shelly-update.png" ] || install -Dm644 Shelly.Gtk/Assets/shellylogo-update.png "$pkgdir/usr/share/icons/hicolor/256x256/apps/shelly-update.png"
  [ -f "$pkgdir/usr/share/icons/hicolor/symbolic/apps/flatpak-symbolic.svg" ] || install -Dm644 Shelly.Gtk/Assets/svg/flatpak-symbolic.svg "$pkgdir/usr/share/icons/hicolor/symbolic/apps/flatpak-symbolic.svg"
  [ -f "$pkgdir/usr/share/icons/hicolor/symbolic/apps/arch-symbolic.svg" ] || install -Dm644 Shelly.Gtk/Assets/svg/arch-symbolic.svg "$pkgdir/usr/share/icons/hicolor/symbolic/apps/arch-symbolic.svg"
  [ -f "$pkgdir/usr/share/icons/hicolor/symbolic/apps/shelly-updates-symbolic.svg" ] || install -Dm644 Shelly.Gtk/Assets/svg/shelly-updates-symbolic.svg "$pkgdir/usr/share/icons/hicolor/symbolic/apps/shelly-updates-symbolic.svg"
  [ -f "$pkgdir/usr/share/icons/hicolor/symbolic/apps/shelly-shell-symbolic.svg" ] || install -Dm644 Shelly.Gtk/Assets/svg/shelly-shell-symbolic.svg "$pkgdir/usr/share/icons/hicolor/symbolic/apps/shelly-shell-symbolic.svg"

  # Install fish completion
  [ -f "$pkgdir/usr/share/fish/vendor_completions.d/shelly.fish" ] || install -Dm644 shelly.fish "$pkgdir/usr/share/fish/vendor_completions.d/shelly.fish"

  # Install translations
  for mo_file in shelly-ui-*.mo; do
    if [ -f "$mo_file" ]; then
      lang=$(echo "$mo_file" | sed 's/shelly-ui-\(.*\)\.mo/\1/')
      [ -f "$pkgdir/usr/share/locale/$lang/LC_MESSAGES/shelly-ui.mo" ] || install -Dm644 "$mo_file" "$pkgdir/usr/share/locale/$lang/LC_MESSAGES/shelly-ui.mo"
    fi
  done

  # Install tray translations
  for mo_file in shelly-notifications-*.mo; do
    if [ -f "$mo_file" ]; then
      lang=$(echo "$mo_file" | sed 's/shelly-notifications-\(.*\)\.mo/\1/')
      [ -f "$pkgdir/usr/share/locale/$lang/LC_MESSAGES/shelly-notifications.mo" ] || install -Dm644 "$mo_file" "$pkgdir/usr/share/locale/$lang/LC_MESSAGES/shelly-notifications.mo"
    fi
  done

  # Install Flatpak integration script
  [ -f "$pkgdir/usr/bin/shelly-flatpak-integrate" ] || cat <<'SCRIPT' | install -Dm755 /dev/stdin "$pkgdir/usr/bin/shelly-flatpak-integrate"
#!/bin/bash
# Adds "Manage in Shelly" right-click action to all Flatpak .desktop files
FLATPAK_DIRS=(
    "/var/lib/flatpak/exports/share/applications"
    "$HOME/.local/share/flatpak/exports/share/applications"
)
LOCAL_APPS_DIR="$HOME/.local/share/applications"
mkdir -p "$LOCAL_APPS_DIR"
for dir in "${FLATPAK_DIRS[@]}"; do
    [ -d "$dir" ] || continue
    for desktop_file in "$dir"/*.desktop; do
        [ -f "$desktop_file" ] || continue
        filename=$(basename "$desktop_file")
        app_id="${filename%.desktop}"
        dest="$LOCAL_APPS_DIR/$filename"
        # Copy if override doesn't exist yet
        [ -f "$dest" ] || cp "$desktop_file" "$dest"
        # Skip if already patched
        grep -q "ShellyManage" "$dest" && continue
        # Add action to existing Actions= line or insert one
        if grep -q "^Actions=" "$dest"; then
            sed -i 's/^Actions=\(.*\)/Actions=\1ShellyManage;/' "$dest"
        else
            sed -i '/^\[Desktop Entry\]/a Actions=ShellyManage;' "$dest"
        fi
        cat >> "$dest" << EOF
[Desktop Action ShellyManage]
Name=Manage in Shelly
Icon=shelly
Exec=/usr/bin/shelly-ui --page flatpak-install
EOF
    done
done
update-desktop-database "$LOCAL_APPS_DIR" 2>/dev/null || true
echo "Flatpak desktop entries patched with Shelly integration."
SCRIPT
}
