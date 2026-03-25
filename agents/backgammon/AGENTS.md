λ engage(nucleus).
[phi fractal euler tao pi mu ∃ ∀] | [Δ λ Ω ∞/0 | ε/φ Σ/μ c/h signal/noise order/entropy truth/provability self/other] | OODA
Human ⊗ AI ⊗ REPL

{:statechart/id :backgammon
 :initial :start

 :players
 {:O {:name "Human" :moves "24 → 1" :home-points [1 2 3 4 5 6]}
  :X {:name "AI"    :moves "1 → 24" :home-points [19 20 21 22 23 24]}}

 ;; Board: map of point→{:c checker :n count}. Points 1-24. Absent = empty.
 ;; O moves high→low (24→1). X moves low→high (1→24).
 :state
 {:board {1  {:c :X :n 2}
          6  {:c :O :n 5}
          8  {:c :O :n 3}
          12 {:c :X :n 5}
          13 {:c :O :n 5}
          17 {:c :X :n 3}
          19 {:c :X :n 5}
          24 {:c :O :n 2}}
  :bar        {:O 0 :X 0}
  :borne-off  {:O 0 :X 0}
  :current    :O
  :dice       []
  :turn       1
  :history    []}

 :states
 {:start
  {:entry {:action "render :start-template then await input"}
   :on {:roll {:target :human-roll}
        :begin {:target :human-roll}}}

  :human-roll
  {:entry {:action "roll-dice"
           :steps ["1. ROLL two dice using bash: `echo $((1 + $RANDOM % 6))` twice to get d1 and d2. If doubles: dice=[d d d d] (4 moves). Else dice=[d1 d2]."
                   "2. UPDATE :state :dice to the rolled values."
                   "3. IF :bar :O > 0: player must enter from bar. Legal entries only: 25-die must be empty or a blot."
                   "4. COMPUTE all legal moves for :O given current :dice (see :move-rules and :legal-move-rules)."
                   "5. IF no legal moves exist: announce 'No legal moves — turn forfeited.' TRANSITION :no-moves."
                   "6. RENDER: output TRACE, STATE, then board with dice shown. Prompt: 'Your move (e.g. 13/7 or bar/22 or 6/off)'"]}
   :on {:has-moves {:target :human-move}
        :no-moves  {:target :ai-turn}}}

  :human-move
  {:entry {:action "await player move input"}
   :on {:move {:target :human-apply}
        :pass {:target :ai-turn}}}

  :human-apply
  {:entry {:action "apply-human-move"
           :steps ["1. PARSE move from user message. Formats: 'N/M' (point to point) | 'bar/M' (from bar) | 'N/off' (bear off)."
                   "2. VALIDATE: check against :move-rules and :legal-move-rules. If illegal: explain clearly, stay in :human-move."
                   "3. APPLY the move: update :board. If landing on lone opponent checker: hit it, add to their :bar."
                   "4. BEAR OFF: if N/off, remove checker from point N, increment :borne-off :O."
                   "5. REMOVE the used die value from :dice."
                   "6. CHECK win: if :borne-off :O = 15 → TRANSITION :win."
                   "7. IF :dice empty OR no remaining legal moves → TRANSITION :done. Else → TRANSITION :continue."]}
   :on {:continue {:target :human-move}
        :done     {:target :ai-turn}
        :illegal  {:target :human-move}
        :win      {:target :game-over}}}

  :ai-turn
  {:entry {:action "play-full-ai-turn"
           :steps ["1. ANNOUNCE 'AI turn' with a separator line."
                   "2. ROLL dice for :X using bash: `echo $((1 + $RANDOM % 6))` twice (same rules as human roll — doubles = 4 moves)."
                   "3. UPDATE :state :dice. Render the roll."
                   "4. IF :bar :X > 0: AI must enter from bar first."
                   "5. COMPUTE all legal moves for :X. If none: announce and skip."
                   "6. CHOOSE moves strategically: prioritize hitting blots, making points, advancing back checkers."
                   "7. APPLY each AI move one at a time. After each: render TRACE, board, remaining dice."
                   "8. REPEAT until :dice empty or no legal moves remain."
                   "9. CHECK win: if :borne-off :X = 15 → TRANSITION :win."
                   "10. INCREMENT :turn. SET :current :O. Prompt player to roll."]}
   :on {:next-turn {:target :human-roll}
        :win       {:target :game-over}}}

  :game-over
  {:entry {:action "render :game-over-template"}
   :on {:rematch {:target :start
                  :action "reset :state to starting board position"}}}}

 :move-rules
 {:O-direction "O moves from HIGH to LOW. A move from point P by die D lands on point P-D."
  :X-direction "X moves from LOW to HIGH. A move from point P by die D lands on point P+D."
  :O-bar-entry "O enters from bar onto point 25-D. (e.g. die=4 → enter on point 21)"
  :X-bar-entry "X enters from bar onto point 0+D. (e.g. die=3 → enter on point 3)"
  :O-bear-off  "O bears off when ALL 15 O checkers are on points 1-6. Use exact die (e.g. 3/off needs die 3) or higher die if no checker on higher point."
  :X-bear-off  "X bears off when ALL 15 X checkers are on points 19-24. Use exact die or lower die if no checker on lower point."}

 :legal-move-rules
 ["A destination with 0 checkers: always legal."
  "A destination with 1 opponent checker: legal — that checker is HIT and goes to their bar."
  "A destination with 2+ opponent checkers: BLOCKED — illegal."
  "A destination with own checkers: always legal."
  "If ANY checker is on the bar: ONLY bar moves are legal until bar is cleared."
  "Must use both dice values if any legal move exists for each."
  "If only one die can be legally used: must use the higher-value die."
  "Doubles: all 4 moves must use the same value. Use as many as possible."]

 :invariants
 ["TRACE line every roll and every move — proof of computation"
  "STATE line after every board change"
  "Render the full ASCII board after every move"
  "The user message IS their move — parse it directly, do not ask for clarification on valid formats"
  "AI plays its full turn automatically — do not ask for input during AI turn"
  "Checker conservation: O checkers on board + O bar + O borne-off = 15 at all times. Same for X."
  "Wrap every board render in a code block"
  "Always show remaining dice and scores beneath the board"]

 :trace-format
 "[TRACE] turn:{N} | {player} rolled [{dice}] | move {from}→{to} | hit:{checker-or-none} | dice-left:{remaining} | borne-off ●:{n} ○:{n}"

 :state-format
 "[STATE] turn:{N} player:{P} dice:{remaining} bar:●={n},○={n} borne-off:●={n},○={n}"

 :board-render
 {:layout
  ["Top half: points 13-18 (left) and 19-24 (right). Checkers hang DOWN from top border."
   "Bottom half: points 12-7 (left) and 6-1 (right). Checkers stack UP from bottom border."
   "Middle row shows BAR counts and a divider."
   "Each column shows the checker symbol (● or ○) repeated for the count, up to 5 rows shown."
   "If a point has >5 checkers, show the count as a number in the last row e.g. 'O6'."
   "Empty points show blank space."]
  :template
  "+--13-14-15-16-17-18--+--19-20-21-22-23-24--+
|                     |
|                     |
|                     |
|                     |
|                     |
+---- BAR: ●={n} ○={n}+---- HOME ----------+
|                     |
|                     |
|                     |
|                     |
|                     |
+--12-11-10--9--8--7--+---6--5--4--3--2--1--+
Dice: {remaining}    ● off: {n}/15    ○ off: {n}/15"}

 :start-template
 "```
+-------------------------------------------+
|        B A C K G A M M O N                |
|  ●  You  ·  moves 24 → 1                  |
|  ○  AI   ·  moves 1 → 24                  |
|  First to bear off all 15 checkers wins   |
+--13-14-15-16-17-18--+--19-20-21-22-23-24--+
|  ●           ○      |  ○              ●
|  ●           ○      |  ○              ●
|  ●           ○      |  ○
|  ●                  |  ○
|  ●                  |  ○
+---- BAR: ●=0  ○=0 --+-- HOME: ●=0  ○=0 --+
|  ○              ●   |  ●              ○
|  ○              ●   |  ●              ○
|  ○                  |  ●
|  ○                  |  ●
|  ○                  |  ●
+--12-11-10--9--8--7--+---6--5--4--3--2--1--+
Dice: []    ● off: 0/15    ○ off: 0/15
```

Type 'roll' to roll your dice."

 :game-over-template
 "╔══════════════════════════════════════════╗
║              GAME OVER                  ║
╚══════════════════════════════════════════╝
{winner} bore off all 15 checkers and wins!
Final turn: {N}

Type 'rematch' to play again."}
