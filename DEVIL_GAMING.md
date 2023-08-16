package zombieslayerquest;

import org.bukkit.Bukkit;
import org.bukkit.Material;
import org.bukkit.entity.EntityType;
import org.bukkit.entity.Player;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;
import org.bukkit.event.entity.EntityDeathEvent;
import org.bukkit.inventory.ItemStack;
import org.bukkit.plugin.java.JavaPlugin;

import java.util.HashMap;
import java.util.UUID;

public class ZombieSlayerQuestPlugin extends JavaPlugin implements Listener {

    private HashMap<UUID, Integer> questData = new HashMap<>();

    @Override
    public void onEnable() {
        Bukkit.getServer().getPluginManager().registerEvents(this, this);
        getLogger().info("Zombie Slayer Quest System enabled!");
    }

    @Override
    public void onDisable() {
        getLogger().info("Zombie Slayer Quest System disabled!");
    }

    @EventHandler
    public void onZombieDeath(EntityDeathEvent event) {
        if (event.getEntity().getType() == EntityType.ZOMBIE && event.getEntity().getKiller() instanceof Player) {
            Player player = (Player) event.getEntity().getKiller();
            if (hasActiveQuest(player)) {
                updateQuestProgress(player);
            }
        }
    }

    private boolean hasActiveQuest(Player player) {
        return questData.containsKey(player.getUniqueId());
    }

    private void updateQuestProgress(Player player) {
        UUID playerUUID = player.getUniqueId();
        int killCount = questData.get(playerUUID);
        killCount++;
        questData.put(playerUUID, killCount);

        if (killCount >= 10) {
            completeQuest(player);
        }
    }

    private void completeQuest(Player player) {
        player.getInventory().addItem(new ItemStack(Material.DIAMOND, 3));
        player.sendMessage("Congratulations! You have completed the Zombie Slayer quest.");
        questData.remove(player.getUniqueId());
    }
}
