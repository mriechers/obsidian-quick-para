> [!WARNING]
> **Archived legacy codebase — not maintained, not installable.**
>
> `obsidian-para-visualizer` was superseded by **obsidian-quick-para**. This branch exists only to preserve
> the original source and its full commit history; it receives no fixes,
> no releases, and no support. Nothing here is wired into the active plugin.
>
> For working code, see the `main` branch of this repository.

---

# PARA Visualizer Plugin

An interactive visualization plugin for Obsidian that brings your PARA-organized vault to life with beautiful charts, graphs, and insights. Includes both **vault-level** and **note-specific** visualizations.

## Features

### 🔀 Two Viewing Modes

**🗂️ Vault View** - Analyze patterns and trends across your entire vault
**📝 Current Note View** - Deep dive into a specific note's context and connections

Toggle between modes using the buttons at the top of the visualizer panel.

### 🎨 Vault-Level Visualizations

1. **Activity Heatmap** 📅
   - GitHub-style calendar heatmap showing note creation and modification activity
   - Separate heatmaps for each PARA location (Inbox, Projects, Areas, Resources, Archive)
   - Click cells to see which notes were active on specific dates
   - Color intensity shows activity level

2. **Knowledge Graph** 🕸️
   - Interactive force-directed graph of your notes and their connections
   - Nodes colored by PARA location
   - Node size reflects number of backlinks
   - Click nodes to open notes
   - Smooth physics-based animation

3. **PARA Flow** 🌊
   - Sankey diagram showing note movement between PARA locations
   - Visual representation of your knowledge management workflow
   - Track how notes transition from inbox to projects to archive

4. **Task Analytics** ✅
   - Task completion metrics by PARA location
   - Task velocity trends over time
   - Open vs. completed task breakdown
   - Identify bottlenecks in your workflow

5. **Review Radar** 📍
   - Radar chart that shows how each PARA bucket tracks against desired review cadences
   - Highlights overdue notes and surfaces the stalest areas/projects/resources
   - Honors optional `review_interval`/`review_every` frontmatter per note (falls back to PARA defaults)
   - Quickly jump to overdue files with a single click

6. **Pipeline Timeline** 📈
   - Stacked timeline of where notes live (Inbox → Projects → Areas → Resources → Archive)
   - Uses `para_history` to show movement volume and stage durations
   - Highlights busiest days, top transitions, and the slowest stage in your pipeline
   - Average time-in-stage cards make bottlenecks obvious

7. **Task Load Calendar** 🗓️
   - 4-week calendar grid of tasks with due dates, color-coded by PARA location
   - Summaries for overdue tasks, upcoming week load, and busiest day
   - Quick links to open overdue tasks directly from the panel
   - Shows load distribution per PARA bucket across the visible window

8. **Tag Cloud** 🏷️
   - Dynamic word cloud of your most-used tags
   - Font size reflects tag frequency
   - Opacity shows recent activity vs. historical usage
   - Click tags to search vault
   - Excludes system tags (all, inbox, projects, etc.)

9. **Statistics Dashboard** 📊
   - Vault overview metrics (total notes, tags, links)
   - PARA distribution with percentages
   - Recent activity tracking (24h, 7d, 30d, 90d)
   - Top 10 most-used tags
   - Average links per note

### 📝 Note-Specific Visualizations

Switch to **Current Note View** to see:

1. **Note Context** 🔍
   - **Incoming Links** (⬅️): Notes that link TO this note (what references this?)
   - **Outgoing Links** (➡️): Notes that this note links TO (what does this reference?)
   - **Sibling Notes** (👥): Other notes in the same folder
   - **Related by Tags** (🔗): Notes sharing content tags (excludes system tags like `all`, `inbox`, `projects`)
   - **Orphan Detection** (⚠️): Warning if note has no connections
   - **Quick Stats**: Visual metrics for link counts and relationships
   - **Click to Navigate**: Click any linked note to open it

2. **PARA History** 🌊
   - **Current Location**: See where the note lives now
   - **Movement Timeline**: Chronological history of PARA location changes with dates
   - **Flow Diagram**: Visual representation of locations the note has visited
   - **Movement Statistics**: Total moves, locations visited, first/last move dates
   - **Automatic Tracking**: The Quick PARA plugin records movements when you relocate files

3. **Note Tasks** ✅
   - Task completion progress bar
   - Open vs. completed task breakdown
   - Due date highlighting (overdue tasks in red)
   - Completion date tracking
   - Task counts and percentages

The note view automatically updates when you switch to a different note or modify the current note.

### ⚙️ Additional Features

- **Time Range Filtering**: View data for last 30 days, 90 days, 6 months, 1 year, or all time
- **Real-time Refresh**: Update visualizations on-demand to reflect latest vault changes
- **PARA-Aware**: Automatically detects and visualizes notes by PARA location using the `para` frontmatter property
- **Beautiful Design**: Follows Obsidian's theme colors and supports both light/dark modes
- **Fast Performance**: Efficient data processing even for large vaults
- **Click-to-Open**: Click elements in visualizations to open related notes or trigger searches

## Dependencies

### Required for Full Functionality

**Quick PARA Plugin** (recommended)
- Provides automatic PARA location tracking (`para` property)
- Records movement history (`para_history` property)
- Required for PARA History visualization
- Vault-level visualizations work without it, but won't show PARA-specific insights

**Without Quick PARA:**
- ✅ Vault visualizations still work (heatmap, graph, tag cloud, stats)
- ✅ Note context view works (links, siblings, related notes)
- ⚠️ PARA History view will show a dependency warning
- ⚠️ PARA-based filtering/coloring won't work without `para` property

### Checking Dependencies

Run the command **"PARA Visualizer: Check Dependencies"** from the command palette to verify Quick PARA is installed and enabled.

## Installation

### From Obsidian Community Plugins (Coming Soon)
1. Open Settings → Community Plugins
2. Search for "PARA Visualizer"
3. Click Install, then Enable

### Manual Installation

1. Download the latest release from [Releases](https://github.com/MarkOnFire/obsidian-para-visualizer/releases)
2. Extract to your vault's `.obsidian/plugins/para-visualizer/` folder
3. Restart Obsidian or reload the app
4. Go to Settings → Community plugins → Enable "PARA Visualizer"
5. **(Recommended)** Install and enable the **[Quick PARA](https://github.com/MarkOnFire/obsidian-quick-para)** plugin for full functionality

## Usage

### Opening the Visualizer

**Option 1: Ribbon Icon**
- Click the bar chart icon (📊) in the left ribbon

**Option 2: Command Palette**
- Press `Cmd/Ctrl + P`
- Type "Open PARA Visualizer"
- Press Enter

The visualizer will open in the right sidebar by default.

### Navigating Visualizations

Use the tabs at the top to switch between visualization modes:
- **📅 Activity Heatmap** - See your vault activity over time
- **🕸️ Knowledge Graph** - Explore note connections
- **🏷️ Tag Cloud** - Discover your most-used tags
- **📊 Statistics** - View vault metrics and insights

### Controls

**Time Range Selector**
- Choose how far back to include notes (30 days to all time)
- Affects all visualizations except Statistics (which shows multiple time ranges)

**🔄 Refresh Button**
- Manually refresh data to reflect latest vault changes
- Useful after creating, moving, or deleting many notes

### Interaction

**Activity Heatmap**
- Hover over cells to see date and activity count
- Click cells to see a popup list of notes modified that day

**Knowledge Graph**
- Drag to pan the view
- Click nodes to open the corresponding note
- Wait for physics simulation to settle (100 iterations)

**Tag Cloud**
- Hover to see tag count and recent activity
- Click tags to open global search for that tag

## Requirements

This plugin works best with vaults using:
- **PARA Method** organization (Inbox, Projects, Areas, Resources, Archive)
- **`para` frontmatter property** to track location (set by the Quick PARA plugin)
- Regular use of tags and wikilinks

### Expected Frontmatter Format

```yaml
---
tags: [pbswi, urgent]
para: projects
created: 2024-01-15
---
```

The plugin will:
- Use the `para` property to determine PARA location
- Extract tags from both frontmatter and inline `#tags`
- Track file creation/modification timestamps
- Map wikilink connections

### Optional Review Cadence Metadata

- Add `review_interval`, `review_every`, or `review` to your frontmatter to specify the desired review cadence for that note (supports numbers in days or strings like `2w`, `1 month`, `weekly`)
- Notes without an explicit interval fall back to sensible PARA defaults (Inbox 2d, Projects 7d, Areas 30d, Resources 90d, Archive 180d)
- The Review Radar uses these values to determine health scores and overdue status

### Due Dates for Task Calendar

- Add due dates in your tasks using the standard `📅 YYYY-MM-DD` syntax so the Task Load Calendar can place them on the grid
- The calendar highlights overdue, current-week, and future tasks, and groups them by PARA location

## How It Works

### Data Collection

The plugin scans your vault and collects:
- All markdown files and their metadata
- PARA locations from `para` frontmatter property
- Tags from frontmatter and inline tags
- File creation and modification dates
- Wikilink connections between notes

### Visualization Algorithms

**Activity Heatmap**
- Groups note modifications by date
- Calculates activity intensity (0-4 levels)
- Renders grid with 3px spacing for readability

**Knowledge Graph**
- Uses simple force-directed layout algorithm
- Repulsion force prevents node overlap (strength: 100)
- Link attraction pulls connected nodes together (target distance: 50px)
- Center gravity keeps graph from drifting (strength: 0.001)
- Runs 100 simulation iterations for stable layout

**Tag Cloud**
- Filters out system tags (all, inbox, projects, etc.)
- Calculates font size based on tag frequency (12px-48px range)
- Sets opacity based on recent vs. historical activity (0.5-1.0)
- Limits to top 50 tags for performance

**Statistics**
- Aggregates vault-wide metrics
- Calculates percentages and averages
- Sorts tags by frequency

## Customization

### PARA Colors

The plugin uses a color-coded scheme for PARA locations:

- **Inbox**: Purple (#8b5cf6)
- **Projects**: Blue (#3b82f6)
- **Areas**: Green (#10b981)
- **Resources**: Orange (#f59e0b)
- **Archive**: Gray (#6b7280)

These colors are defined in `main.js` (line 5) and can be customized by editing the `PARA_COLORS` object.

### Performance Tuning

For very large vaults (>5000 notes), you can improve performance by:

1. **Reduce time range**: Use 30 or 90 days instead of "All time"
2. **Limit graph nodes**: Edit `main.js` line 394 to filter notes by link count
3. **Adjust simulation**: Reduce iterations from 100 to 50 (line 462)

## Troubleshooting

### "No notes found in selected time range"

**Cause**: No notes have been modified within the selected time period
**Solution**: Expand the time range or use "All time"

### Graph appears static/not animated

**Cause**: Browser may have paused animation loop
**Solution**: Click the refresh button to restart the simulation

### Wrong PARA location detected

**Cause**: Note missing `para` property in frontmatter
**Solution**:
- Use the Quick PARA plugin's "Update PARA tags for all files" command
- Manually add `para: projects` (or appropriate location) to frontmatter

### Tags not appearing in Tag Cloud

**Cause**: System tags (all, inbox, projects, etc.) are filtered out
**Solution**: These tags are intentionally hidden; only content tags are shown

### Heatmap cells are all the same color

**Cause**: Activity levels may be similar across dates
**Solution**: This is normal; hover to see exact counts. Different PARA locations may show more variation.

## Development

### Project Structure

```
para-visualizer/
├── main.js          # Plugin core and all visualization logic
├── manifest.json    # Plugin metadata
├── styles.css       # All styling and theming
└── README.md        # This file
```

### Modifying Visualizations

All visualization logic is in `main.js`:
- `renderHeatmap()` - Line 230
- `renderGraph()` - Line 321
- `renderTagCloud()` - Line 490
- `renderStats()` - Line 543

### Color Scheme

PARA colors are defined at the top of `main.js` (line 5) in the `PARA_COLORS` object.

### Adding New Visualizations

1. Add a new tab to the `tabs` array in `renderTabs()` (line 138)
2. Add a case to the switch statement in `render()` (line 114)
3. Create a new `render*()` method with your visualization logic
4. Add corresponding styles to `styles.css`

## Compatibility

- **Obsidian Version**: 0.15.0+
- **Desktop**: ✅ Yes
- **Mobile**: ✅ Yes (with reduced canvas performance)
- **Live Preview**: ✅ Compatible
- **Themes**: ✅ Adapts to all themes (uses CSS variables)

## Future Enhancements

Potential features for future versions:
- Export visualizations as PNG/SVG
- 3D graph view option
- Timeline view showing vault evolution
- Sankey diagram for PARA flow (Inbox → Projects → Archive)
- Integration with Dataview queries
- Custom color schemes via settings
- Filtering by specific tags or folders
- Animation replay of vault growth

## Credits

Created by Mark Riechers for the MarkBrain PARA vault.

Built with:
- Obsidian API
- Canvas API for graph rendering
- CSS Grid for heatmap layout
- Vanilla JavaScript (no external dependencies)

## License

MIT License - Feel free to modify and share!

## Feedback

Found a bug or have a feature request? Open an issue in the repository or submit a pull request.

---

**Happy visualizing! 📊✨**
