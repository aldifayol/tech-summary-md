# 💾 Database Sharding Came From UO - Article Summary

This article by Raph Koster details the origin of the term **"sharding"** in database architecture, suggesting it originated within the development of the groundbreaking MMORPG **Ultima Online (UO)** in the late 1990s.

---

## 🌎 The Problem: Scaling the World

* **UO's Initial Setup:** To handle the massive scale of an early MMORPG, the game was built around multiple independent game worlds, or servers, each running a full copy of the game state.
* **Need for Parallel Worlds:** The developers anticipated that a single server wouldn't be able to handle the number of players they expected, necessitating a system of parallel worlds.
* **The Crystal Metaphor:** The game's backstory provided a ready-made metaphor: in UO's lore, the world of Britannia was shattered into pieces (shards of a crystal). This led the team to refer to each separate game world as a **"shard."**

---

## 💻 Technical Implementation (The Link to Databases)

* **Database Partitioning:** While the game servers themselves were separate, they all connected to a common **Oracle database** for centralized information storage (e.g., player account data, billing, and global settings).
* **Separating Game Data:** Crucially, the data for the active game world—including player characters, inventory, and objects—was stored in databases that were **partitioned and isolated** to their specific game server (shard). This partitioning, combined with the name "shard," served the purpose of horizontal scaling for the most demanding part of the database load.
* **The Naming Spreads:** The development team, including Koster, used the term "shard" internally for both the game worlds and their associated partitioned databases. Over time, this term was adopted by other developers in the industry, especially as other web-scale applications faced similar database scaling challenges.

---

## 💡 Conclusion

The article concludes that while the technical concept of horizontal partitioning of databases existed before UO, the modern, widely recognized term **"sharding"** was popularized and derived directly from the terminology used by the **Ultima Online** team to name their separated game worlds and their underlying database structures.
