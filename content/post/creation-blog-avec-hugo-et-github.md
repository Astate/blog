---
title: "Création d'un blog avec hugo et github"
date: 2019-01-23T21:09:16-05:00
tags: [hugo,static-site, github]
draft: false 
---


J'ai construit ce blog en utilisant Github pages, [Hugo](https://gohugo.io) le theme [beautifulhugo](https://themes.gohugo.io/theme/beautifulhugo). La partie la plus étonnante est qu’il est gratuit, minimaliste, utilise Markdown et qu’il peut être exécuté en un rien de temps! De plus, Hugo est livré avec Live-Reload activé afin que vous puissiez voir simultanément les modifications que vous apportez sur votre site Web.

Voici un court tutoriel sur la création de ce blog. Commençons!

**Cette page est très inspiré de [https://vaibhavk.github.io/post/beautiful_blog/ ] (https://vaibhavk.github.io/post/beautiful_blog/)**

**Note: Si cela ne vous intéresse pas de configurer vous-même, insérez simplement le [repo de vaibhavk ] (https://github.com/vaibhavk/Blogs) et apportez les modifications nécessaires pour inclure vos informations.**


### 1. Github
Pour cette étape, vous devez avoir un compte github et git installés sur votre système. Ensuite, rendez-vous [ici](https://github.com/new) pour créer un nouveau repository pour votre blog. Vous pouvez nommer n'importe quoi. Vous devez maintenant cloner ce repository en utilisant:
{{< highlight terminal >}}
$ git clone https://github.com/yourusername/repo-you-just-made.git && cd repo-you-just-made
{{< /highlight >}}
Maintenant nous pouvons initialiser hugo dans le répertoire courtant. vous devez ajouter le paramètre --force car sans celui-ci vous aurez un message qui vous dira que le répertoire n'est pas vide.
{{< highlight terminal >}}
        $ hugo new site . --force
{{< /highlight >}}
Nous n'avons plus besoin du dossier **Public** dans notre code source git, car il est généré par hugo pour la construction du site. Pour faire ça :
{{< highlight terminal >}}
        $ echo "Public/" > .gitignore
{{< /highlight >}}
Envoyer vos changement à Github :
{{< highlight terminal >}}
        $ git add --all
        $ git commit -m "initial commit" -a
        $ git push -u origin master
{{< /highlight >}}

### 2. Theme
Nous allons maintenant utiliser un thème. Vous pouvez utiliser le thème que j'utilise [beautifulhugo](https://themes.gohugo.io/theme/beautifulhugo)  ou choisir parmi les  [Themes Hugo](https://themes.gohugo.io), selon la procédure que vous préférez, mais la procédure restera la même. Pour ajouter le thème:

{{< highlight terminal >}}
  $ git submodule add https://github.com/halogenica/beautifulhugo.git themes/beautifulhugo
{{< /highlight >}}

Nous avons maintenant besoin d’un fichier de configuration. Pour ce faire, nous pouvons copier le fichier de configuration du site exemple dans les thèmes.
{{< highlight terminal >}}
        $ cp themes/beautifulhugo/exampleSite/config.toml config.toml
{{< /highlight >}}

Maintenant, notre site de base est presque prêt. Pour voir vos progrès, vous pouvez faire :
{{< highlight terminal >}}
        $ hugo serve -w
{{< /highlight >}}
Et aller dans votre navigateur internet à cette adresse [localhost:1313](http://localhost:1313).

Vous pouvez voir beaucoup de choses non pertinentes ici. Nous devons mettre à jour notre fichier config.toml afin qu'il corresponde à votre profil. Vous pouvez prendre une référence du  [config.toml](https://gist.github.com/vaibhavk/2b17b4d0839d039fcb3f7837b09855b3) de vaibhavk

Vous avez également besoin que votre propre logo soit affiché ici, pas celui fourni par le thème. Pour ce faire, créez un répertoire img dans un dossier statique et copiez-y le fichier.:
{{< highlight terminal >}}
        $ mkdir -p static/img
        $ cp /path-to-image static/img/avatar-icon.png
{{< /highlight >}}


Les dates ne sont pas encore supporté par Hugo pour le mutltilanguage. La page [customize-dates](https://gohugo.io/content-management/multilingual/#customize-dates)  du site de Hugo donne une façon de remédier à cette problématique.

La première étape est de créer *data/mois.yaml* avec le contenu suivant:
{{< highlight terminal >}}
1: "janvier"
2: "février"
3: "mars"
4: "avril"
5: "mai"
6: "juin"
7: "juillet"
8: "août"
9: "septembre"
10: "octobre"
11: "novembre"
12: "décembre"
{{< /highlight >}}

Dans le cas du thème beautifulhugo, vous devez modifier *post_meta.html* pour traduire le mois.
{{< highlight terminal >}}
        $ vi /themes/beautifulhugo/layouts/partials/post_meta.html
{{< /highlight >}}

Avant :
{{< highlight go-html-template "linenos=inline,hl_lines=6 9-10,linenostart=1"  >}}

  <i class="fas fa-calendar"></i>&nbsp;{{ $datestr | i18n "postedOnDate"}}

{{< / highlight >}}

Après : 
{{< highlight go-html-template "linenos=inline,hl_lines=6 9-10,linenostart=1"  >}}

  <i class="fas fa-calendar"></i>&nbsp;{{ i18n "postedOnDate"}}{{ .Date.Day }} {{ index $.Site.Data.mois (printf "%d" .Date.Month) }} {{ .Date.Year }}

{{< / highlight >}}




Nous sommes maintenant prêts à écrire notre tout premier blog.

### 3. Première page du Blog
Nous pouvons utiliser Hugo pour initialiser le premier fichier de notre blog et le modifier en utilisant:
{{< highlight terminal >}}
        $ hugo new post/first-blog.md
        $ vi content/!$
{{< /highlight >}}

Maintenant, vous pouvez écrire votre blog ici. Lorsque vous avez terminé votre blog, changez _draft = true_ en _draft = false_ que vous pouvez utiliser sur les toutes premières lignes.
**N'oubliez pas que jusqu'à brouillon = true, la page ne sera pas affichée sur votre site Web.**

Maintenant, vérifiez votre site en utilisant:
{{< highlight terminal >}}
        $ hugo serve -w
{{< /highlight >}}

Maintenant, si vous pouvez déployer notre site sur github.

### 4. Github Encore
Retournez sur github et créez un autre repository avec **yourusername.github.io** comme vaibhavk à fait _vaibhavk.github.io_ et cloner le.
Maintenant que vous êtes dans votre **Blog's repository**. Vérifiez votre site Web localement une fois de plus :
{{< highlight terminal >}}
        $ hugo serve -w
        $ hugo -d /path-to-yourusername.github.io/
{{< /highlight >}}

Maintenant, tout le contenu a été ajouté, prêt à être validé (commit).
{{< highlight terminal >}}
        $ cd /path-to-yourusername.github.io
        $ git add --all
        $ git commit -m "rebuilding site `date`" -a
        $ git push origin master
{{< /highlight >}}

Aller à cette adresse _https://yourusername.github.io_ via le navigateur internet.

### 5. Conclusion


#### Références
* [Vaibhavk Blog](https://vaibhavk.github.io/post/beautiful_blog/)
* [Horea's Blog](https://horeaporutiu.github.io/blog/how-to-blog-w/-hugo-and-github-pages/)
* [Hugo's Documentaion](https://gohugo.io/getting-started)

#### Closing Notes
Finalement, Merci à Vaibhavk pour cette article clair.

