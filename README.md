
Forked version of [unledis' HologramLib](https://github.com/unldenis/Hologram-Lib). If you are looking to use a Hologram library use their library as this library will be stripped of most of it's features to be tailor made for private projects. If you are upstream or run an upstream fork feel free to use any changes that you wish to.

# Hologram-Lib
Asynchronous, high-performance Minecraft Hologram library for 1.8-1.19 servers.
## Requirements
This library can only be used on spigot servers higher or on version 1.8.8. The plugin <a href="https://www.spigotmc.org/resources/protocollib.1997/">ProtocolLib</a> is required on your server.

## Example usage
```java
public class ExampleHolograms implements Listener {

    private final Plugin plugin;
    private final HologramPool hologramPool;
    /**
     * @param plugin The plugin which uses the lib
     */
    public ExampleHolograms(@NotNull Plugin plugin) {
        this.plugin = plugin;
        this.hologramPool = new HologramPool(plugin, 70, 0.5f, 5f);
    }

    /**
     * Appends a new Hologram to the pool.
     *
     * @param location  The location the Hologram will be spawned at
     * @param excludedPlayer A player which will not see the Hologram for 10 seconds
     */
    public void appendHOLO(@NotNull Location location, Player excludedPlayer) {
        // building the NPC
        Hologram hologram = Hologram.builder()
                .location(location)
                .addLine("Hello World!", false)
                .addLine("Using Hologram-Lib", false)
                .addLine("Hello %%player%%", true)
                .addLine(new ItemStack(Material.IRON_BLOCK))
                .addPlaceholder("%%player%%", Player::getName)
                .build(hologramPool);

        hologram.getLines().get(3).setAnimation(Animation.AnimationType.CIRCLE);
        // simple changing animating block and text
        timingBlock(hologram);

        if (excludedPlayer != null) {
            // adding the excluded player which will not see the Hologram
            hologram.addExcludedPlayer(excludedPlayer);

            // shows 10 seconds after theHologram
            Bukkit.getScheduler().runTaskLater(plugin, () -> hologram.removeExcludedPlayer(excludedPlayer), 20L * 10);
        }
    }

    private final static Queue<Material> materials = new ArrayDeque<>() {
        {
            addAll(Arrays.asList( Material.IRON_BLOCK, Material.GOLD_BLOCK, Material.DIAMOND_BLOCK, Material.EMERALD_BLOCK));
        }
    };


    /**
     * Update the block and the first line of text of the hologram
     * @param hologram The hologram to update
     */
    private void timingBlock(Hologram hologram) {
        new BukkitRunnable() {
            final ItemLine itemLine = (ItemLine) hologram.getLines().get(3);
            @Override
            public void run() {
                Material mat = materials.poll();
                itemLine.set(new ItemStack(mat));
                materials.offer(mat);
            }
        }
        .runTaskTimer(plugin, 30L, 30L);
    }
}
```
