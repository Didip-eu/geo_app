# geo_app

Place-of-issue review app for the Didip-eu/georeferencing data. The data and the annotators' decisions stay in that private repository; this repository only hosts the app for GitHub Pages. The app and this guide are maintained in that repository (app/) and copied here by update_app.sh.

---

# Place of issue review

Our little browser tool for checking that each charter's **place of issue** name matches its coordinates because the `placeofissuing_geonames.tsv` come from GeoNames and may be wrong. The page runs on GitHub Pages. Annotators log in with a personal GitHub token, and the page saves their decisions into this repository through the GitHub API.

- **Part 1 – Annotator guide:** how to use the tool, step by step.
- **Part 2 – Coordinator guide:** setup, configuration, branches, the merge, and reverting.

---

# Part 1 – Annotator guide

## 1. Getting a token

The page saves your work to GitHub in your name. For that it needs a **personal access token**, a kind of password that only allows access to this project. You also need **write access** to the repository: ask the coordinator to add you as a collaborator.

**Option 1 (recommended): a fine-grained token**
1. Open [github.com/settings/personal-access-tokens/new](https://github.com/settings/personal-access-tokens/new).
2. *Token name*: anything, e.g. "place review". *Expiration*: e.g. 90 days.
3. *Resource owner*: the organization that owns the repository (`Didip-eu`).
4. *Repository access*: *Only select repositories*, then choose this repository.
5. *Permissions → Repository permissions → Contents*: **Read and write**.
6. Click *Generate token* and copy it. It starts with `github_pat_`. GitHub shows it only once.

If the organization isn't offered as resource owner, or the token stays "pending", an owner of the organization has to allow or approve fine-grained tokens. Until then, use option 2.

**Option 2 (fallback): a classic token**
1. Open [github.com/settings/tokens/new](https://github.com/settings/tokens/new).
2. Tick the scope **`repo`** (or `public_repo` if the repository is public), and set an expiration date.
3. Generate and copy it. It starts with `ghp_`.
4. If the organization uses single sign-on (SSO), click *Configure SSO* next to the token in the token list and authorize it for the organization.

A classic token can reach **all** your repositories, not only this one. Keep it private.

## 2. First login

1. Open the page (the address comes from the coordinator).
2. Paste the token into the login window and click **Log in**.
3. The token is kept only in this browser. On a shared computer, click **Log out** in the GitHub panel when you finish.

If the login fails, the window says why:
- the text is not a token;
- the token has no access to this repository (use the other option, or ask for approval);
- you have no write access (ask the coordinator). **Continue read-only** lets you look around in the meantime.

## 3. What the screen shows

**Left: the sidebar**
- **Title line:** *Export* and *Settings*, and *Collapse all / Expand all*.
- **Panels:** each opens and closes by clicking its header. A closed panel shows a one-line summary. A panel opens by itself, with a red summary, when something needs attention: a save error, GitHub rate limits, or a read-only login.
  - **GitHub:** your login, when your work was last saved, what is waiting to be saved, and the buttons *Save now*, *Refresh others' work*, *Undo* and *Log out*.
  - **Automatic check:** the OpenStreetMap comparison, see step 4. The line *Name search in: …* shows which countries the name search covers. **change** (or *Settings*) lets you set your own two-letter country codes, e.g. `hu` or `it,at,de`; empty means the whole world. Your choice (kept in your browser):
    - limits the **list**, *Next undecided* and the jump after a decision to places whose `country` in the source is one of your countries (a *My countries* chip appears; click it to see all places again). Names are matched to codes: `Czech Republic` and `Czechia` are `cz`, `England`, `Scotland`, `Wales` are `gb`; `Austria and Hungary` counts for both.
    - makes the **suggestions** in the detail view (the OSM list and the diamonds on the map) come from your countries.
    - The shared automatic check (the verdicts and the export) keeps the project's setting, so a narrow search never overwrites the others' results. **Use the default** goes back to the project's setting.
  - **Progress:** how many places are decided.
  - **Filters:**
    - *Automatic*: All, Problems, Outliers, Mismatch, Near, Name not found, No coordinates, Match, Not checked.
    - *Decision*: My countries (when set in *Settings*), Mine, All, Not decided, Correct, Corrected, Uncertain, No place, Conflicts.
    - Also "Only the visible map area" and "Sort by".
- **Search box:** always visible, above the list.
- **The list:** one row per **place group**, i.e. all documents with the same place name and the same coordinates.

**Right: the map**
- **Dots (automatic check result):** green *match*, yellow *near*, red *mismatch*, grey *name not found*, blue *not checked yet*.
- **Decided places:** rings in the colour of the decision (green correct, purple corrected, pink uncertain).
- **Focus mode:** while a place is open, the map shows only that place and related places, so wrong positions stand out.
  - Same-name places elsewhere are connected to it by dashed lines with the distance.
  - Places more than `t2` km away (25 km by default) get an **orange ring**.
  - *Zoom to place* zooms back in. *Show all points* switches focus mode off.
- **OSM candidates** (the places OpenStreetMap found for the name) are small **diamonds**.
- **Clicking any point** opens a small window with its links and details, and **Open** opens that group.

## 4. The daily workflow

1. **Your share of the work.** If the coordinator has divided the work by country, the **Mine** filter (on by default) shows your countries plus the countries nobody has. The list starts on **Not decided**: anything that any annotator has decided is not shown. So nobody works twice on the same place.
2. **Automatic check.** *Start automatic check* (in the Automatic check panel) looks up every place name in OpenStreetMap and measures how far the nearest place with that name is:
   - **Match**: within 5 km, or OSM names the same place at the point. Usually fine.
   - **Near**: within 25 km. Look closer.
   - **Mismatch**: OSM knows the name, but far away. Often a wrong position.
   - **Name not found**: often an old or Latin form (Jaurinum = Győr, Posonium = Bratislava).
   - **No coordinates**: the source has no position.
   - **Accept matches** marks all automatic matches as correct in one go.
3. **Work through the rest:** use the **Problems** or **Outliers** filter, open the top row, decide, and the next undecided place opens by itself. **Next undecided** jumps there directly.

## 5. Judging a place

- **Sources line** under the title:
  - **Charter / Charters (n)**: the charter pages on Monasterium. Read the original wording.
  - **Google** and **Wikipedia** searches for the name as written.
  - **GeoNames**: the current GeoNames entry.
  - **OSM at the point**: what is really at the coordinates.
- **Name and coordinates:** the OSM candidates. Each has a dark **type badge**: *city*, *town*, *village*, *peak*, *pond*, *river* …. The type is often the key. "Budín" is the Czech name of Buda, but also of Slovak peaks and Czech ponds: a *peak* or *pond* is not where a charter was issued. **OSM** and **Wikipedia** open the candidate itself, and **Use** takes its position.
- **LLM suggestion** (if the project has it, see *LLM suggestions* in Part 2): for historical, Latin or unusual names, click **Ask the LLM**. The model (DeepSeek on the DH Infra server in Graz) gets the name, the charter identifiers (archive, fonds, date) and the earlier automatic geocoding marked as unreliable, and proposes modern places with a short reason. The answer takes 30–60 seconds; you can work on meanwhile, a message tells you when it arrives. Every candidate is checked in OSM (the nearest place of that name in its country, within 50 km) and, through Wikidata, for a GeoNames ID. "Not found in OSM" means that the position is only the model's estimate, and a GeoNames ID "given by the LLM (not checked)" may be invented. **Use** moves the point there and fills in the GeoNames ID and the country; save it with a decision as usual. The model can be wrong: it is a hint, not an answer. Answers are shared, so everybody sees the answer once somebody has asked.
- **Outliers:** a place whose position is far (more than 25 km) from where most documents with the same name are. It's usually a mis-geocode, sometimes a different place with the same name.
- **The lists** in the detail view:
  - **Same name, here**: the same name (ignoring accents, brackets, "(?)" …) within 25 km. These are almost always the same place.
  - **Same name, far away**: the same name farther away. Either a mis-geocode, or a different place with the same name.
  - **Other names nearby**: other spellings within a few km, e.g. *Jaurinum*, *Raab* and *Győr* at the same spot.
  - **Same word elsewhere**: names anywhere in the data that contain a distinctive word of the checked names (the matched word is shown). This finds spellings like "ležení u Györu (Rab)".
- **Quick links** on every row: the charter, Google and Wikipedia.

## 6. Decisions

At the bottom of the detail view:
- **Name and coordinates match** (*Correct*): the position is right.
- **Save new position** (*Corrected*): first set the right position. Drag the pink marker, click an OSM diamond, use **Place by clicking**, or paste coordinates or a Google Maps / OSM link into the position box.
- **Uncertain**: you are not sure. The source position stays in the data, and your proposal is kept separately.
- **No place of issue**: the charter names no place (*sine loco*, *b. m.*, *o. O.*, *s. l.*, *ohne Ort*). Its coordinates and GeoNames ID are emptied in the data; the country stays, unless you type another one in the Country field first. Use it also when such a charter was given coordinates by mistake (e.g. `o. O.` at Rome). For a single charter of a group, use *This document was issued elsewhere* first. If the place can be inferred (e.g. `b. m. [Řím]`), use *Save new position* with the inferred place instead and explain it in the note.
- **GeoNames ID:** correct it in the field if needed. The **geonames.org** link shows what the number means.
- **Country:** when you set a new position, the app looks up its country in OpenStreetMap and fills in the field (unless you typed one). If you save before the answer arrives, the country is added to your decision when it does. For a saved position whose country differs from OpenStreetMap, a **Set country to …** button appears. **Save note, ID, country** saves the note, the GeoNames ID and the country without changing the decision.
- **Note:** write down your source or reasoning. It ends up in the data.
- **Document-level exceptions:** if one charter of a group was issued elsewhere, click **This document was issued elsewhere** under that document and decide it separately. A document's decision beats its group's.
- **Decided per document:** if every document of a group has its own decision, nothing is left at the group's position. The group shows *Decided per document*, leaves the *Not decided* list and the map, and needs no decision of its own.

## 7. Confirm as one place

The box at the top of the detail view makes one **gazetteer place** out of all spellings of the same real place, so that they all get the same coordinates and GeoNames ID.

1. **Check the rows** that are the same place. "Same name, here" is checked already. Tick others from the lists, or:
   - **Select on the map:** click **Select on map** (or press **S**) and drag a rectangle. **Alt**+drag draws a free shape, and **Ctrl**+drag unchecks. **Shift+click** a point to check or uncheck it. **Esc** ends the selection.
   - **Add by name:** type a spelling, e.g. "raab", and click **Add**.
2. **Watch the map:** checked places are filled, unchecked ones hollow. Orange means more than 5 km from the pin, red more than 25 km. The line above the lists says e.g. "12 checked · 2 more than 5 km away · 1 outlier". Click it to highlight those rows.
3. **Canonical name:** e.g. "Győr" or "Vienna". It's proposed from OSM or the most frequent spelling; change it if needed. After selecting many rows, check it again: it follows the checked spellings.
4. **GeoNames ID:** proposed from the checked rows. **Look up** asks OSM and Wikidata. Always confirm it with the geonames.org link.
5. **Existing places:** if a place with the same GeoNames ID or a similar name nearby already exists, the box offers **Add to "…"**. Use that instead of creating a duplicate.
6. **Confirm as one place** (or Enter). Everything is saved in one step, and the next unassigned name opens.

A group that already belongs to a place shows **Place: …** with a **Place settings** link: rename, move, change the ID or country, or delete the place.

## 8. Saving

- **You don't need to save by hand.** Your work is saved **60 seconds after your last change**, at once when 25 or more changes are waiting, when you switch to another tab, and when you close the tab.
- **The status line** in the GitHub panel shows "Saved 14:32", or e.g. "3 unsaved · autosave in 42 s".
- **Save now** saves immediately.
- **If GitHub slows us down** (rate limit), the panel turns red: "GitHub is rate-limiting saves; will retry at 14:40". Nothing is lost: your changes are kept in the browser, and the save happens by itself later.
- **After closing the tab**, anything that couldn't be sent in time is saved the next time you open the page in the same browser.
- Other annotators' work is loaded every minute, or at once with **Refresh others' work**.

## 9. When someone else already decided

- **A banner** "Decided by *login* on *date*: *decision*" appears when you open a place someone else decided. The decision buttons are locked. If you are sure it's wrong, click **Override**, write a note saying why, and decide. Your decision is recorded as an override.
- **In Confirm as one place**, rows decided by someone else, or belonging to another annotator's countries, are shown but locked ("decided by …", "assigned to …"). Click **Override** in the row if you really need it.
- **Conflicts:** when two annotators decided the same place differently at the same time, it appears under the **Conflicts** filter. The detail view shows both versions side by side. Click **Keep this one** on the right one.

## 10. History, Undo, Revert

- **History:** the History section of a place lists every change to it and to its gazetteer place, by every annotator, newest first: who, when, and what changed.
- **Revert to this state:** puts the place back to how it was after that change. "Before any change" removes the decision. The revert gets an automatic note, and can itself be undone.
- **Undo** (GitHub panel, or **Ctrl+Z**) undoes your own last action, including a whole "Confirm as one place". Press it again to redo. The last 50 actions of the session can be undone.

## 11. Keyboard shortcuts

| Key | What it does |
|---|---|
| **Ctrl+Z** (Cmd+Z on a Mac) | Undo your last action (again: redo) |
| **S** | Select on map on / off |
| drag / **Alt**+drag / **Ctrl**+drag | While selecting: rectangle / free shape / uncheck |
| **Shift**+click on a point | Check or uncheck it for the place being confirmed |
| **Esc** | Leave the selection mode, stop "Place by clicking", or close the detail view |
| **Enter** | In the position box: move the point. In the Confirm box: confirm |
| **Tab**, then **Enter** / **Space** | Open or close a panel |

## 12. FAQ

- **OSM says "Budín" is a pond / a peak.** Many names are also ordinary words or names of hills and ponds. Look at the type badge and the charters: a charter is issued in a town, a castle, a monastery or a camp, not on a pond. Here the right place is Buda (Budapest).
- **The same name is two different places** (Frankfurt am Main and Frankfurt an der Oder). Don't put them into one place: leave the far group unchecked, and confirm each Frankfurt separately with its own canonical name, e.g. "Frankfurt am Main". If only one charter of a group belongs to the other Frankfurt, use a document-level exception.
- **I closed the tab. Is my work lost?** No. It is saved when you close the tab. If that didn't finish, the browser kept it, and it's saved the next time you open the page in the same browser.
- **"Decided by …" – but it's wrong.** Click **Override**, give your reason in the note, and decide. Or ask the other annotator first.
- **I confirmed the wrong places.** Press **Ctrl+Z** right away, or use **Revert** in the History later.
- **A place has no coordinates.** Find it (charter, Google, Wikipedia, OSM), set the position, and save it as *Corrected*.
- **I moved all documents of a place elsewhere, but the place is still listed.** Once every document has its own decision, the group shows *Decided per document* and needs nothing more. You don't have to delete anything.
- **The document list stops at 80.** Click **Show 250 more** or **Show all** below the list.
- **Why can't I tick a row?** It's decided by, or assigned to, someone else (see step 9).

---

# Part 2 – Coordinator guide

## What the tool produces

| Branch | Content |
|---|---|
| `main` | The code (`app/`, `.github/workflows/`) and the source data (`placeofissuing_geonames.tsv`). |
| `annotations` | The results only. **The app creates it** on the first save, as a copy of the source branch. |

On the `annotations` branch:

| Path | Content |
|---|---|
| `annotations/decisions/<login>.json` | One annotator's decisions (`edits`) and change log (`log`). Everyone has their own file. |
| `annotations/gazetteer/<login>.json` | One annotator's gazetteer places (`places`) and change log (`log`). |
| `annotations/auto/<login>.json` | Automatic check results, shared so that nobody re-runs them. |
| `annotations/output/placeofissuing_geonames_reviewed.tsv` | The merged result (see below). |
| `annotations/output/gazetteer.tsv` | One row per place: `id`, `name`, `lat`, `lon`, `geonameId`, `country`, `variant_names` (all spellings assigned, `|`-separated), `group_count`, `doc_count`, `by`, `at`. |
| `annotations/output/changes.tsv` | Every logged change: `at`, `by`, `action` (decide / override / revert / undo / confirm_place / accept_matches), `level` (place / document / gazetteer), `key`, `placeName`, `before_status`, `after_status`, `before_lat`, `before_lon`, `after_lat`, `after_lon`, `before_geonameId`, `after_geonameId`, `place_id`, `note`, `batch`. |

Rules:
- **Newest wins:** if several annotators decide the same group, the newest record wins.
- **Decider:** the decider is always the owner of the file (`<login>.json`).
- **Change log:** each change is appended to its owner's log as `{key, before, after, at, action, batch}`. Steps of one action (a confirm, an accept, an undo) share a batch id.

### Output columns
- **Decided rows:** they get the decided values in the native columns: `latitude` / `longitude` (when moved), `geonameId` / `country` (when set), `checked.by` (login), `checked.when` (`YYYY-MM-DD`) and `checked.notes`. Their source values go to `review_original_lat`, `review_original_lon`, `review_original_geonameId` and `review_original_country`.
- **No place rows** (`no_place`): `latitude`, `longitude` and `geonameId` are empty; `country` keeps the source value unless the decision sets one; the `review_auto_*` columns are empty.
- **Uncertain rows:** they keep the source values. The proposal goes to `review_proposed_lat`, `review_proposed_lon` and `review_proposed_geonameId`.
- **Rows assigned to a gazetteer place:** they get `review_place_id` and `review_canonical_name`.
- **Unchanged:** rows without a decision are unchanged. `placeName`, `Old lat` and `Old long` are never changed.
- **Every row** also gets:
  - `review_status` (`not_decided` / `correct` / `corrected` / `uncertain` / `no_place`) and `review_level` (`place` / `document`);
  - `review_auto_result` (`match` / `near` / `mismatch` / `name_not_found` / `no_coordinates` / `not_checked`);
  - `review_auto_distance_km`, `review_auto_nearest_match`, `review_auto_nearest_lat`, `review_auto_nearest_lon` and `review_auto_osm_at_point`.
  - The `review_auto_*` columns describe the automatic check of the **source** position. They are empty when a decision moved the row (the new position was not checked; its source values are in `review_original_*`).
- **Which level decided a row:** `review_level = document` means that charter was decided individually; `place` means it follows the decision on its group (same `placeName` + source coordinates).
- **The app's Export button** writes the same file, byte for byte. It keeps the source delimiter.

## Setup (once)

1. **Hosting the page.**
   - **Recommended:** put the `app/` folder (`index.html`, `config.json`) into a small, separate **public** repository and serve it with GitHub Pages. This is free and uses no Actions minutes. Its `config.json` points to the data repository (`"repo": "Didip-eu/georeferencing"`), which can stay **private**. The page reads and writes the data through the API with each annotator's own token.
   - **Alternative:** serve this repository itself (*Settings → Pages*: branch `main`, folder `/ (root)`). The address is `https://<owner>.github.io/<repo>/app/`, and everything on `main`, including the TSV, becomes public. `.nojekyll` at the root switches off Jekyll.
2. **Actions.** *Settings → Actions → General → Workflow permissions*: *Read and write permissions*.
3. **Annotators.** *Settings → Collaborators* (or an organization team), at least **Write**. Fine-grained tokens must be allowed (or approved) for the organization.
4. **`app/config.json`:**
   - `repo`: `"owner/repo"` (empty: taken from the Pages address).
   - `branch`: where the data file is read from. `dataBranch`: where results go (`annotations`).
   - `csv`, `encoding`: the data file.
   - `columns`: the columns for name, latitude, longitude, ID, GeoNames ID, country and `checked.*`.
   - `derived`: builds the charter link and list title from `atomid`.
   - `nominatim`: `countrycodes`, `t1` / `t2` (km limits for match / near), `lang`, `email`. `countrycodes` is used by the shared automatic check; each annotator can set their own in *Settings* for the suggestions in the detail view. `t1` / `t2` and `lang` stay fixed for everybody.
   - `nearbyKm`: radius of "Other names nearby".
   - `decisionsDir`, `autoDir`, `gazetteerDir`, `output`: paths on the data branch.
   - `llm` (optional): `model`, `dir` (on the data branch, default `annotations/llm`), `endpoint`. Without `llm` the *LLM suggestion* section is hidden. See *LLM suggestions*.
   - **`assignments`** (optional): work allocation by the values of the `country` column. Countries not listed for anyone are open to everyone. Without `assignments` everybody sees everything.
     ```json
     "assignments": {
       "alice": ["Austria", "Hungary"],
       "bob":   ["Italy"]
     }
     ```

## Merge workflow

`.github/workflows/merge-decisions.yml` refreshes the output **twice a day** (05:00 and 17:00 UTC), or on demand (*Actions → Merge review decisions → Run workflow*, with a *force* option).
- **Skipping idle runs:** a run first checks with `git log` whether anything under `annotations/decisions`, `annotations/auto` or `annotations/gazetteer` changed since the last output commit. If not, it stops within seconds.
- **Branches:** the source and data branches are set at the top of the workflow file (`SOURCE_BRANCH`, `DATA_BRANCH`).
- **Default branch only:** scheduled workflows only run from the default branch, so this becomes active once the workflow is merged to `main`. Until then, run the merge by hand.

## LLM suggestions

The *Ask the LLM* button sends the prompt that the app builds to the DH Infra API (University of Graz, OpenAI-compatible, `https://api.dhinfra.uni-graz.at/v1/chat/completions`). The app parses and checks the answer itself. There are two ways to deliver the request:

- **Through a GitHub Action** (`"endpoint": ""`, the current setting). The browser can't call the API directly: the server does not allow cross-origin (CORS) requests from GitHub Pages. So the app commits the request to `annotations/llm/requests/<login>.json`, and `.github/workflows/llm-suggest.yml` runs `app/scripts/llm_suggest.py`, which answers into `annotations/llm/results/<login>.json` (a failed request goes under `errors`, so an earlier answer stays). The app looks for the answer every 15 seconds and gives up after 5 minutes. Each request is one short Actions run, and the key never reaches the browser.
  1. **Secret:** *Settings → Secrets and variables → Actions → New repository secret*, `DHINFRA_API_KEY` = the DH Infra key.
  2. **Workflow on the data branch:** a push runs the workflow file of the pushed branch. So copy `.github/workflows/llm-suggest.yml` to the `annotations` branch, at the same path. The copy on `main` only allows a manual run, which answers whatever is still open.
  3. **Script:** the workflow reads `app/scripts/llm_suggest.py` from `SOURCE_BRANCH` (top of the workflow file, `main`). Set it to `tamas` while the script is only on `tamas`.
- **Directly from the browser** (`"endpoint": "https://api.dhinfra.uni-graz.at/v1/chat/completions"`), once DH Infra allows CORS for `https://didip-eu.github.io`. The answer comes in seconds and is saved to `results/<login>.json` by the app. The key is read with the annotator's GitHub token from `annotations/llm/key.json` (`{"key": "…"}`) on the private data branch. **Never put the key into `config.json`**: the app repository and its Pages site are public.

By hand, e.g. to answer open requests locally: `DHINFRA_API_KEY=… python3 app/scripts/llm_suggest.py --dir <checkout of annotations>/annotations/llm` (`--max`, `--max-tokens`, `--url`). The model's reasoning counts against `max_tokens` (12 000): a cut-off answer is reported as an error.

## Running the merge by hand

This costs nothing. Run it at the root of a checkout of the data branch:

```
git fetch origin main annotations
git worktree add ../georef-annotations annotations
cd ../georef-annotations
git show origin/main:app/scripts/merge_decisions.py > /tmp/merge_decisions.py
python /tmp/merge_decisions.py --ref origin/main --config app/config.json
```

- `--ref` reads the TSV and `config.json` from that git ref.
- The decision, auto and gazetteer files are read from the working tree.
- The script writes the output TSV, `gazetteer.tsv` and `changes.tsv` into `annotations/output/`. It prints warnings for decisions that point to a deleted place, and for decisions that match no row (the name or the coordinates changed in the source TSV).
- **How rows are found:** a group decision stores the group's `placeName` and source coordinates (`n`, `la`, `lo`) and applies to every row that has them. A document decision stores the charter's atomid (`id`) and beats its group's decision. So don't edit `placeName`, `latitude` or `longitude` in the source TSV while the review runs.

## Testing the merge locally

`app/scripts/test_merge_local.py` runs the merge on the current data branch without touching the checkout or GitHub. It works in a temporary directory (deleted at the end), so run `git fetch origin` first:

```
git fetch origin
python3 app/scripts/test_merge_local.py --samples 10
```

- It extracts `annotations/` from `origin/<dataBranch>` and the TSV from `origin/<branch>` (both from `app/config.json`; override with `--data-ref` / `--source-ref`).
- It prints a summary per annotator (decisions, statuses, places, log actions), runs `merge_decisions.py`, and then checks every output row independently.
- It exits with 0 when there are no problems. `--out DIR` keeps the output files, `--keep-temp` keeps the temporary directory, and `--no-verify` skips the check.

## Reverting at git level

**Prefer the app first:** annotators can undo and revert inside the app (History, Undo), and those corrections are logged. For bigger clean-ups, the coordinator can work at git level: **every save is one commit** on the data branch, named e.g. "alice: 12 decisions, 1 place, 30 auto results".

- **Undo one save:** `git revert <commit>` on the `annotations` branch. Later saves of the same user touching the same file may make the revert conflict; then use the next option.
- **Put a user's file back to an older state:** `git checkout <older-commit> -- annotations/decisions/<login>.json` (and/or the gazetteer file), then commit and push.
- **Important:** the app keeps each annotator's unsaved and recent changes in their browser, and re-saves any that are newer than what is on GitHub. Before a git-level revert of someone's work, ask them to close the app. Afterwards they must clear the app's data in that browser (site data for the page), otherwise their browser may save the reverted records again.
- **Refresh the output:** run the merge (by hand or with the workflow's *force* option).

## Testing the `tamas` branch before it is merged

GitHub Pages serves `main`, so test the branch locally. **The app writes to GitHub**, so use a throwaway data branch.

1. Push `tamas`.
2. In `app/config.json` on `tamas`, set `"branch": "tamas"` and a throwaway `"dataBranch"` (e.g. `"annotations-test"`).
3. In the repository root, run `python -m http.server 8000` and open <http://localhost:8000/app/>. The data file is read from the `tamas` branch through the API.
4. The Action can't run from `tamas`. Test the merge by hand, with `--ref origin/tamas`.
5. Delete the throwaway branch afterwards. Before merging, set `branch`, `dataBranch`, `SOURCE_BRANCH` and `DATA_BRANCH` back.

## Notes on the data file

- **Quoting:** the file is tab-separated, one record per line. A field is only unquoted when it is a well-formed quoted field (`"Paris (""Actum Parisius"")"`). A stray quote such as `"i3n` stays as it is instead of swallowing the next rows. The app and the merge script read and write TSV the same way.
- **Broken header:** the header joins `geonameId   checked.by  checked.when    checked.notes` with spaces. Both readers split it back into four columns, because that is the only way the header matches the data rows.
- **No coordinates:** `unknown` / `unbekannt` or an empty value in `latitude` / `longitude` means no coordinates.
- **Size:** the file on `main` is about 88 MB. The app downloads it from its `download_url` (raw.githubusercontent.com, compressed), because the REST API breaks off long downloads after about 10 s ("Content-Length header of network response exceeds response Body"). If that fails twice, it falls back to the API. GitHub's API limit for a file is 100 MB; above it, the file would have to be split or compressed.


Readme by Claude Code custom model 2026.09.25
