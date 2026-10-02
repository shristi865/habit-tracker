<?php
// Two small forms in one page: ?cat=ID adds a habit to that category, ?new=1 adds a category.
require 'db.php'; $u = me();
$cat = q("SELECT * FROM categories WHERE id=? AND user_id=?", "ii", (int)($_GET['cat'] ?? 0), $u)[0] ?? null;
if (!$cat && !isset($_GET['new'])) { header('Location: dashboard.php'); exit; }
$name = trim($_POST['name'] ?? '');
if ($_SERVER['REQUEST_METHOD'] === 'POST' && $name !== '') {
  if ($cat) q("INSERT INTO habits(user_id,cat_id,name,target) VALUES(?,?,?,?)", "iiss", $u, $cat['id'], $name, trim($_POST['target'] ?? ''));
  else q("INSERT INTO categories(user_id,name) VALUES(?,?)", "is", $u, $name);
  header('Location: dashboard.php'); exit;
}
top('Add'); ?>
<form method="post" class="card narrow">
<?php if ($cat): ?>
  <h3>Add habit to <?= e($cat['name']) ?></h3>
  <input name="name" id="hname" placeholder="Habit name" required><input name="target" placeholder="Target (e.g. 30 min)">
  <p>Ideas: <span class="chip">Play guitar</span> <span class="chip">Meditate</span> <span class="chip">Stretch</span> </p>
  <button class="btn">Add habit</button>
<?php else: ?>
  <h3>Add category</h3><input name="name" placeholder="Category name" required><button class="btn">Add category</button>
<?php endif; ?>
  <a class="btn alt" href="dashboard.php">Cancel</a>
</form>
<?php bottom(); ?>

