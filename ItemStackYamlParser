/*
  <- SUPPORTED FORMAT ->
  items:
   UniqueSortName:
    material:
    name:
    amount:
    lore:
    enchants:
 */

import org.bukkit.ChatColor;
import org.bukkit.Material;
import org.bukkit.configuration.ConfigurationSection;
import org.bukkit.configuration.file.FileConfiguration;
import org.bukkit.enchantments.Enchantment;
import org.bukkit.enchantments.EnchantmentWrapper;
import org.bukkit.inventory.ItemStack;
import org.bukkit.inventory.meta.ItemMeta;

import java.util.*;
import java.util.stream.Collectors;

class ItemStackYamlParser {
    private final FileConfiguration file;
    private final String pathToItems;
    private List<ItemStack> itemStackList;

    public ItemStackYamlParser(final FileConfiguration fileConfiguration, final String pathToItems) {
        this.file = fileConfiguration;
        this.pathToItems = pathToItems;
        this.itemStackList = new ArrayList<>();
    }

    public void parseItems() {
        loadItems();
    }

    public List<ItemStack> getItemStackList() {
        return new ArrayList<>(itemStackList);
    }

    public List<ItemStack> getItemStacksByMaterial(Material material) {
        return itemStackList.stream().filter(itemStack ->
                        itemStack.getType().equals(material))
                .collect(Collectors.toList());
    }

    public List<ItemStack> getItemStacksByEnchants(Enchantment enchantment) {
        return itemStackList.stream().filter(itemStack ->
                        itemStack.getEnchantments().containsKey(enchantment))
                .collect(Collectors.toList());
    }


    private Set<String> getSection(String path) {
        ConfigurationSection section = file.getConfigurationSection(path);

        if (section == null) throw new NullPointerException("Section cannot be null");

        return section.getKeys(false);
    }

    private void loadItems() {
        getItemSection().forEach(key ->
                itemStackList.add(createItem(pathToItems + "." + key)));
    }

    private Set<String> getItemSection() {
        return getSection(pathToItems);
    }

    private ItemStack createItem(String path) {
        ItemStack itemStack = new ItemStack(getMaterial(path), getAmount(path));
        String itemName = getName(path);
        List<String> lore = getLore(path);
        Map<Enchantment, Integer> enchantments = getEnchantments(path);
        ItemMeta itemMeta = itemStack.getItemMeta();


        if (itemName != null) {
            itemMeta.setDisplayName(formatText(itemName));
        }

        itemMeta.setLore(formatText(lore));

        itemStack.setItemMeta(itemMeta);

        itemStack.addUnsafeEnchantments(enchantments);

        return itemStack;
    }

    private Material getMaterial(String path) {
        String materialName = file.getString(path + ".material");

        if (materialName == null) throw new NullPointerException("Material cannot be null.");

        Optional<Material> opMaterial = Optional.ofNullable(Material.getMaterial(materialName.toUpperCase()));

        if (! opMaterial.isPresent()) throw new IllegalArgumentException("This material does not exist.  (" + materialName + ").");

        return opMaterial.get();
    }

    private int getAmount(String path) {
        return file.getInt(path + ".amount");
    }

    private String getName(String path) {
        return file.getString(path + ".name");
    }

    private List<String> getLore(String path) {
        return new ArrayList<>(file.getStringList(path + ".lore"));
    }

    private Map<Enchantment, Integer> getEnchantments(String path) {
        Set<String> enchantmentsSection = getEnchantSection(path);

        Map<Enchantment, Integer> enchantments = new HashMap<>();

        enchantmentsSection.forEach(en -> {
            System.out.println(en);
            enchantments.put(EnchantmentWrapper.getByName(en.toUpperCase()),
                    file.getInt(path + ".enchants." + en));
        });

        return enchantments;
    }

    private Set<String> getEnchantSection(String path) {
        return getSection(path + ".enchants");
    }

    private String formatText(String toFormat) {
        return ChatColor.translateAlternateColorCodes('&', toFormat);
    }

    private List<String> formatText(List<String> toFormat) {
        return toFormat.stream()
                .map(this::formatText)
                .collect(Collectors.toList());
    }
}
