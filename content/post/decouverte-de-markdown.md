---
title: "Decouverte De Markdown"
date: 2019-01-25T10:18:03-05:00
tags: [hugo,content,makdown,static-site]
draft: false
---

Markdown est un langage de balisage léger créé par John Gruber et [Aaron Swartz](http://fr.wikipedia.org/wiki/Aaron_Swartz "Aaron Swartz sur Wikipedia").
Son but est de fournir une syntaxe facile à lire et à écrire. En plus d'être facile, c'est très rapide.
Lorsqu'on écrit avec Markdown, nous ne devons pas se donner l'impression
qu'il a été marqué par des balises ou des instructions de formatage. Ça devrait être naturel.

Un avantage majeur de Markdown est de ne pas avoir à vous soucier de la forme.
Combien de fois vous avez fini de rapports ou des présentations parce que vous avez passé 80% du temps de façonner trois phrases?
Markdown, vous pouvez contrefoutre de la forme. La chose la plus importante est ce que vous écrivez, c'est le contenu.
Donc ce qui est le plus important, c'est ce que vous y mettez et non la façon qu'on le mets.

Bon je commence, je n'utilise que [vscode](https://github.com/Microsoft/vscode) (un éditeur de texte) pour concevoir cette page.

----

#### Un peu plus sur Markdown

Markdown **génial** parce que:

- il est facile à *écrire*
- il est facile à *lire*
- ça vous permet de garder le focus

----

Les balises < pre > et < code > sont ajouter lorsque que le début des lignes sont indenté d'au moins quatre espaces. 
J'ai utilisé cette astuce pour afficher le code ci-dessous.

{{< highlight html >}}
    Markdown : 
    # Titre
    ## Titre
    
    Html :
    <h1>Titre</h1>
    <h2>Titre</h2>

      
    Makrdown :
    - élément 1 de la liste
    - étément 2 de la liste
    
    Html :
    <ul>
      <li>élément 1 de la liste</li>
      <li>élément 2 de la liste</li>
    </ul>
{{< / highlight >}}

Pour de l'information plus complète vous pouvez aller sur le site officel de Markdown [daringfireball.net](http://daringfireball.net/projects/markdown/basics)
