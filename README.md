## G. Willian Zanckett

# Willian Zanckett

## 🔥 Rank de Desenvolvedor

<!-- RANK_START -->

Carregando...

<!-- RANK_END -->

---

## 📊 Estatísticas

![Stats](https://github-readme-stats.vercel.app/api?username=SEU_USUARIO\&show_icons=true\&theme=dark)

![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=SEU_USUARIO\&layout=compact\&theme=dark)

import os
import requests

USERNAME = os.environ["USERNAME"]
TOKEN = os.environ["TOKEN"]

headers = {
"Authorization": f"token {TOKEN}"
}

# Busca repositórios

repos = requests.get(
f"https://api.github.com/users/{USERNAME}/repos?per_page=100",
headers=headers
).json()

repo_count = len(repos)

# Contagem simples de scripts

script_extensions = [
".py", ".js", ".ts", ".c", ".cpp",
".java", ".cs", ".php"
]

script_count = 0

for repo in repos:
try:
files = requests.get(
f"https://api.github.com/repos/{USERNAME}/{repo['name']}/git/trees/main?recursive=1",
headers=headers
).json()

```
    if "tree" in files:
        for item in files["tree"]:
            if any(item["path"].endswith(ext) for ext in script_extensions):
                script_count += 1

except:
    pass
```

repo_points = min((repo_count / 100) * 50, 50)
script_points = min((script_count / 500) * 50, 50)

score = round(repo_points + script_points)

ranks = [
(100,"SSS+"),
(99,"SSS"),
(98,"SS"),
(95,"S"),
(90,"A+"),
(80,"A"),
(70,"B+"),
(60,"B"),
(50,"C+"),
(40,"C"),
(30,"D+"),
(20,"D"),
(15,"E+"),
(10,"E"),
(5,"F+"),
(0,"F")
]

rank = "F"

for limit, value in ranks:
if score >= limit:
rank = value
break

filled = int(score / 5)
bar = "█" * filled + "░" * (20 - filled)

panel = f"""
╔══════════════════════════════╗
║        RANK {rank:<12}║
╠══════════════════════════════╣
║ Repositórios .... {repo_count:<10}║
║ Scripts ......... {script_count:<10}║
╠══════════════════════════════╣
║ Pontuação ....... {score}/100     ║
║ {bar} ║
╚══════════════════════════════╝
"""

with open("README.md","r",encoding="utf8") as f:
readme = f.read()

start = "<!-- RANK_START -->"
end = "<!-- RANK_END -->"

before = readme.split(start)[0]
after = readme.split(end)[1]

new_readme = before + start + "\n\n`text\n" + panel + "\n`\n\n" + end + after

with open("README.md","w",encoding="utf8") as f:
f.write(new_readme)

name: Atualizar Rank

on:
schedule:
- cron: "0 0 * * *"
workflow_dispatch:

jobs:
update:
runs-on: ubuntu-latest

```
steps:
  - uses: actions/checkout@v4

  - uses: actions/setup-python@v5
    with:
      python-version: "3.12"

  - run: pip install requests

  - run: python scripts/update_rank.py
    env:
      USERNAME: SEU_USUARIO
      TOKEN: ${{ secrets.GITHUB_TOKEN }}

  - run: |
      git config user.name github-actions
      git config user.email github-actions@github.com

      git add README.md

      git diff --staged --quiet || git commit -m "Atualização automática de rank"

      git push
```

