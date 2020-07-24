
# Chess on the Kindle

How to create a very simple diagram for the kindle. For how to create the html to then convert to mobi with calibre [here](https://tex.stackexchange.com/questions/94749/chess-notation-does-not-show-figurine-font):



This is based on the code pgn2latex by Felix Kling. It works really nicely with the nested parentheses. He seems to be the webmaster of [Rybka](http://rybkachess.com.www52.your-server.de/index.php?auswahl=Contact) and I found it [here](https://rybkaforum.net/cgi-bin/rybkaforum/topic_show.pl?tid=32208). 

skak-sample.tex:
```
\documentclass{book}
\usepackage[ps,mover]{skak}
\begin{document}
\parindent=0pt
\newgame
\mainline{1. d4 d5} \mainline{2. c4 Nf6 }
\mainline{3.Ka1 Qa1 4.Ra1 Ba1 5.Na1 a1}
\showboard
\end{document}  
```

skak.4ht:
```
\NewConfigure{showboard}{2}
\let\oldshowboard\showboard
\Configure{showboard}{\Picture*{}}{\EndPicture}
\renewcommand\showboard{\a:showboard\oldshowboard\b:showboard}

\NewConfigure{showinverseboard}{2}
\let\oldshowboard\showinverseboard
\Configure{showinverseboard}{\Picture*{}}{\EndPicture}
\renewcommand\showinverseboard{\a:showinverseboard\oldshowboard\b:showinverseboard}
```


Then just do `htlatex skak-sample "configfile"



This answer for the configuration file for the [clear page](https://tex.stackexchange.com/questions/346811/tex4ebook-paragraph-spacing-centering-and-newpages/346889#346889) command to work:

```
% save the clearpage before it is redefined by tex4ht
\let\oldclrearpage\clearpage
% define macro for newpage insertion
\def\mypagebreak{\Configure{newpage}{\ifvmode\IgnorePar\fi\EndP\HCode{<div class="newpage"></div>}}}
\Preamble{xhtml}
% define it for \newpage
\mypagebreak
\Css{.newpage{page-break-before:always;}}
% modify \Configure{BODY} so our confiurations work on all extracted pages
\Configure{@BODY}{\def\clearpage{\bgroup\mypagebreak\oldclrearpage\egroup}}
\Configure{@/BODY}{\global\let\clearpage\oldclrearpage\Configure{newpage}{}}
\begin{document}
\EndPreamble
```

To create pgn from a pgn in Python so far do:

```python
n = 'uno.txt'
game = gamechap[0]

with open(n,'w') as f:
    mainlinestemp = str(game.mainline_moves())
    fentemp = game.headers['FEN']
    turn = 'w'
    if turn == 'w':
        inv = 'false'
    else:
        inv = 'false'
    f.write(f"\\fenboard{{{fentemp}}}\n")
    f.write('\\begin{center}\n')
    f.write(f"\\showboard \n")

    #f.write(f"\\chessboard[setfen={fentemp},inverse={inv}]\n")
    f.write('\\end{center}\n')
    
    #f.write(f"\\mainline{{{mainlinestemp}}}")
    f.write(f"\\clearpage \n")
    f.write(f"\\newpage \n")

    
    f.write(f"\\mainline{{{mainlinestemp}}}")
    
    f.write(f"\\fenboard{{{fentemp}}}\n")
    f.write('\\begin{center}\n')
    
    f.write(f"\\showboard \n")

    #f.write(f"\\chessboard[setfen={fentemp},inverse={inv}]\n")
    f.write('\\end{center}\n')
    
    f.write(f"\\fenboard{{{fentemp}}}\n")

    
    #Write variations
    if len(game.variations) > 0:
        f.write('\\begin{itemize}\n')
        for var in game.variations:
            f.write(f"\\item \\variation{{{var}}}\n")    
        f.write('\\end{itemize}\n')

    

    
    
    #fboard = f'\\fenboard{maintemp}'
    #f.write(fboard)
    #f.write('\\begin{center}\n')
    #f.write('\\chessboard[setfen={fen}]\n'.format(fen=fentemp))
    #f.write('\\begin{end}\n')
```

I am sure there are better ways ...
