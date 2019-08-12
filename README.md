### percol
---
https://github.com/mooz/percol

```py
function ppgrep() {
  if [[ $1 == "" ]]; then
    PERCOL=percol
  else
    PERCOL="percol --query $1"
  fi
  ps aux | eval $PERCOL | awk '{ print $2 }'
}

function ppkill() {
  if [[ $1 =~ "^-" ]]; then
    QUERY=""
  else 
    QUERY=$1
    [[ $# > 0 ]] && shift
  fi
  ppgrep $QUERY | xarg kill $*
}

function exists { which $1 &> /dev/null } 
if exists percol; then
  function percol_select_history() {
    local tac
    exists gtac && tac="gtac" || { exists tac && tac="tac" || { tac="tail -r" } }
    BUFFER=$(fc -l -n 1 | eval $tac | percol --query "$LBUFFER")
    CURSOR=$#BUFFER
    zle -R -c
  }
  
  zle -N percol_select_history
  bindkey '^R' percol_select_history
fi

function pattach() {
  if [[ $1 == "" ]]; then
    PERCOL=percol
  else
    PERCOL="percol --query $1"
  fi
  
  session=$(tmux ls)
  [ $? -ne 0 ] && return
  
  session$(echo $sessions | eval $PERCOL | cut -d : -f 1)
  if [[ -n "$session" ]]; then
    tmux att -t $session
  fi
}

from cStringIO import StringIO
from percol import Percol
from percol.actions import no_ouput

def main(candidates):
  si, so, se = StringIO(), StringIO(), StringIO()
  with Percol(
      actions=[no_output],
      descriptions={'stdin': si, 'stdout': so, 'stderr': se},
      candidates=iter(candidates)) as p:
    p.loop()
  results = p.models_candidate.get_selected_results_with_index()
  return [r[0] for r in results]

if __name__ == "__main__":
  candidates = ['foo', 'bar', 'baz']
  results = main(candidates)
  print("You picked: {!r}".format(results))

percol.view.PROMPT = ur"<bold><yellow>X / _ / X</yellow></bold>%q"

percol.import_keymap({
  "C-h" : lambda percol: percol.command.delete_backward_char(),
})

percol.view.PROMPT = ur"<blue>Input:</blue> %q"
percol.view.RPROMPT = ur"(%F) [%i/%I]"

percol.view.__class__.PROMPT = property(
  lambda self:
  ur"<bold><blue>QUERY </blue>[:a]:</bold> %q" if percol.model.finder.case_insensitive
  else ur"<bold><green>QUERY </green></bold> %q"
)

percol.view.prompt_replacees["F"] = lambda self, **args: self.model.finder.get_name()
percol.view.RPROMPT = ur"(%F) [%i/%I]"

percol.view.CANDIDATES_LINE_BASIC = ("on_default", "default")
percol.view.CANDIDATES_LINE_SELECTED = ("underline", "on_yellow", "white")
percol.view.CANDIDATES_LINE_MARKED = ("bold", "on_cyan", "black")
percol.view.CANDIDATES_LINE_QUERY = ("yellow", "bold")

from percol.finder import FinderMultiQueryMigemo
from percol.finder import FinderMultiQueryMigemo

from percol.finder import FinderMultiQueryMigemo, FinderMultiQueryRegex
percol.import_keymap({
  "M-c" : lambda percol: percol.command.toggle_case_sensitive(),
  "M-m" : lambda percol: percol.command.toggle_finder(FinderMultiQueryMigemo),
  "M-r" : lambda percol: percol.command.toggle_finder(FinderMultiQueryRegex)
})
```

```sh
percol --match-method regex
parcol --match-method migemo
sudo apt-get install cmigemo
percol --match-method pinyin
```

```
```


