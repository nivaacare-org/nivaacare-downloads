# nivaacare-downloads

The Nivaacare Android apps (APK files), for direct download from the Nivaacare website.

The files are not in the code above. They are under **Releases > "Nivaacare apps (latest)" > Assets**:
https://github.com/nivaacare-org/nivaacare-downloads/releases/tag/apk

## Download addresses (they never change)

| App | Who it is for | Address |
|---|---|---|
| Nivaacare | Patients and families | https://github.com/nivaacare-org/nivaacare-downloads/releases/download/apk/nivaacare.apk |
| Nivaacare Partner | Nurses, physiotherapists, dressers | https://github.com/nivaacare-org/nivaacare-downloads/releases/download/apk/nivaacare-partner.apk |
| Nivaacare Mitra | Local Mitras | https://github.com/nivaacare-org/nivaacare-downloads/releases/download/apk/nivaacare-mitra.apk |

The website uses these as `NEXT_PUBLIC_APK_PATIENT`, `NEXT_PUBLIC_APK_PARTNER` and `NEXT_PUBLIC_APK_MITRA`.

## Putting up a fresh build

Every new build replaces the old file in the same release, so the addresses above stay the same.

1. In the app folder (`nivaacare-app`, `nivaacare-partner` or `nivaacare-mitra`), open `app.config.js` and raise:
   - `version` (for example 0.0.2 to 0.0.3)
   - `android.versionCode` by 1. Android refuses an update whose versionCode is not higher.
2. Build and upload, one app at a time (about 15 to 25 minutes each):

   ```bash
   cd ~/Desktop/nivaacare/nivaacare-web
   bash scripts/release-apk.sh patient    # or: partner, mitra
   ```

   The script builds the APK with EAS (`preview` profile), downloads it, and replaces the file in the `apk` release.
3. On the website, set `NEXT_PUBLIC_VERSION_PATIENT` / `_PARTNER` / `_MITRA` to the new version (in `.env.local` and in Coolify or Vercel as a build variable), then redeploy.

## What the Mac needs

- `eas whoami` shows the `nivaacare` account (member of `nivaacare-org` on Expo).
- `gh auth status` shows an account with write access to this repo.

## If something goes wrong

- `HTTP 408: Upload body timed out`: the build is fine, only the upload was slow. Run the script again, or upload the saved file (its path is in the script output):

  ```bash
  gh release upload apk /path/to/nivaacare.apk -R nivaacare-org/nivaacare-downloads --clobber
  ```

- Check that all three links work (each should print 200):

  ```bash
  for f in nivaacare.apk nivaacare-partner.apk nivaacare-mitra.apk; do echo "$f $(curl -sIL -o /dev/null -w '%{http_code}' https://github.com/nivaacare-org/nivaacare-downloads/releases/download/apk/$f)"; done
  ```
