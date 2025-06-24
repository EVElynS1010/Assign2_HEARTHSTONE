def main() -> None:
    pass


if __name__ == "__main__":
    main()


# Task 1

class Card(object):
Instructor
| 06/08 at 4:07 pm
Grading comment:
class needs docstring

    def __init__(self, **kwargs):
        """Initializes a basic card with default values."""
        self._name = CARD_NAME
        self._description = CARD_DESC
        self._symbol = CARD_SYMBOL
        self._cost = 1
        self._effect = {}
        self._permanent = False

    def __str__(self) -> str:
        return f"{self._name}: {self._description}"

    def __repr__(self) -> str:
        return f"{self.__class__.__name__}()"

    def get_symbol(self) -> str:
        """Returns the symbol of the card."""
        return self._symbol

    def get_name(self) -> str:
        """Returns the name of the card."""
        return self._name

    def get_cost(self) -> int:
        """Returns the cost to play the card."""
        return self._cost

    def get_effect(self) -> dict[str, int]:
        """Returns the effect of the card."""
        return self._effect.copy()

    def is_permanent(self) -> bool:
        """Returns True if the card is a permanent entity (minion)."""
        return self._permanent


# Task 2
class Shield(Card):
    """
        A non-permanent card that applies a shield effect to a target entity.

        The card provides 5 shield points upon use.
        """
    def __init__(self, **kwargs):
Instructor
| 06/08 at 4:08 pm
Grading comment:
Inherited docstring for init is not satisfactory, would need to override it.

        super().__init__()
        self._name = SHIELD_NAME
        self._description = SHIELD_DESC
        self._symbol = SHIELD_SYMBOL
        self._effect = {SHIELD: 5}
        self._permanent = False


# Task 3
class Heal(Card):
    """
        A non-permanent card that restores health to a target entity.

        The card heals 2 health points and costs 2 energy to play.
        """
    def __init__(self, **kwargs):
        super().__init__()
        self._name = HEAL_NAME
        self._description = HEAL_DESC
        self._symbol = HEAL_SYMBOL
        self._cost = 2
        self._effect = {HEALTH: 2}
        self._permanent = False


# Task 4
class Fireball(Card):
    """
        A dynamic spell card that increases its damage the longer it stays in hand.

        Starting with 3 base damage, it adds +1 for each turn in hand.
        Not a permanent card. Costs 3 energy to play.
        """
    def __init__(self, turns_in_hand: int, **kwargs):
        super().__init__()
        self._name = FIREBALL_NAME
        self._description = FIREBALL_DESC
        self._symbol = str(turns_in_hand)
        self._cost = 3
        self._permanent = False
        self._turns_in_hand = turns_in_hand
        self.effect = {DAMAGE: 3 + turns_in_hand}
Instructor
| 06/08 at 3:57 pm
Grading comment:
effect should be a private attribute.


    def get_symbol(self) -> str:
Instructor
| 06/08 at 3:58 pm
Grading comment:
This getter method doesn't need to be overridden because of how your increment_turn works. The inherited get_symbol would work fine :)

        """Returns the number of turns the Fireball has been held as its symbol."""
        return str(self._turns_in_hand)

    def get_effect(self) -> dict[str, int]:
        """Returns the damage effect value based on turns held in hand."""
        return {DAMAGE: 3 + self._turns_in_hand}

    def increment_turn(self):
        """Increases the turns_in_hand by 1 and updates the damage accordingly."""
        self._turns_in_hand += 1
        self._symbol = str(self._turns_in_hand)
        self.effect = {DAMAGE: 3 + self._turns_in_hand}

    def __repr__(self) -> str:
        return f"{self.__class__.__name__}({self._turns_in_hand})"

    def __str__(self) -> str:
        current_damage = 3 + self._turns_in_hand
        return f"{self.__class__.__name__}: {self._description} Currently dealing {current_damage} damage."


# Task 5
class CardDeck(object):
    """Represents a collection of cards forming a player's deck."""
    def __init__(self, cards: list[Card]):
        """Initializes a card deck with a list of cards."""
        self._cards = cards

    def __str__(self):
        return ",".join(card.get_symbol() for card in self._cards)

    def __repr__(self):
        return f"CardDeck([{', '.join(repr(card) for card in self._cards)}])"

    def is_empty(self) -> bool:
        """Returns True if the deck has no more cards."""
        return len(self._cards) == 0

    def remaining_count(self) -> int:
        """Returns the number of cards remaining in the deck."""
        return len(self._cards)

    def draw_cards(self, num: int) -> list[Card]:
        """Draws a specified number of cards from the top of the deck."""
        drawn = self._cards[:num]
        self._cards = self._cards[num:]
        return drawn

    def add_card(self, card):
        """Adds a card to the bottom of the deck."""
        self._cards.append(card)


# Task 6
class Entity(object):
    """Base class for any game entity with health and shield."""
    def __init__(self, health: int, shield: int):
        """Initializes an entity with health and shield values."""
        self._health = health
        self._shield = shield

    def __repr__(self) -> str:
        return f"Entity({self._health}, {self._shield})"

    def __str__(self) -> str:
        return f"{self._health},{self._shield}"

    def get_health(self) -> int:
        """Returns the entity's current health."""
        return self._health

    def get_shield(self) -> int:
        """Returns the entity's current shield."""
        return self._shield

    def apply_shield(self, shield: int):
        """Applies additional shield to the entity."""
        self._shield += shield
        return self

    def apply_health(self, health: int):
        """Heals the entity by the specified amount."""
        self._health += health
        return self

    def apply_damage(self, damage: int):
        """Applies damage to the entity, reducing shield first then health."""
        if damage <= self._shield:
            self._shield -= damage
        else:
            remaining = damage - self._shield
            self._shield = 0
            self._health = max(0, self._health - remaining)
        return self

    def is_alive(self) -> bool:
        """Returns True if the entity is still alive (health > 0)."""
        return self._health > 0


# Task 7
class Hero(Entity):
    """Represents the player or opponent hero in the game."""

    def __init__(self, health: int, shield: int, max_energy: int, deck, hand):
        """Initializes a Hero with health, shield, energy, a deck, and a hand."""
        super().__init__(health, shield)
        self._max_energy = max_energy
        self._energy = max_energy
        self._deck = deck
        self._hand = hand

    def __str__(self):
        stats = f"{self._health},{self._shield},{self._max_energy}"
        deck_str = str(self._deck)
Instructor
| 06/08 at 3:20 pm
Grading comment:
Hungarian notation.

        hand_str = ",".join(card.get_symbol() for card in self._hand)
        return f"{stats};{deck_str};{hand_str}"

    def __repr__(self) -> str:
        return (
            f"Hero({self._health}, {self._shield}, {self._max_energy}, "
            f"{repr(self._deck)}, {repr(self._hand)})"
        )

    def get_energy(self) -> int:
        """Returns the current available energy of the hero."""
        return self._energy

    def spend_energy(self, energy: int) -> bool:
        """Attempts to spend energy. Returns True if successful, False otherwise."""
        if self._energy >= energy:
            self._energy -= energy
            return True
        return False

    def get_max_energy(self) -> int:
        """Returns the hero's maximum energy."""
        return self._max_energy

    def get_deck(self) -> CardDeck:
        """Returns the hero's deck."""
        return self._deck

    def get_hand(self) -> list[Card]:
        """Returns the current hand of the hero."""
        return self._hand

    def new_turn(self):
        """Handles logic for the start of a new turn:
        increments Fireballs, draws cards, increases energy."""
        for card in self._hand:
            if isinstance(card, Fireball):
                card.increment_turn()

        num_add = 5 - len(self._hand)
        draw_cards = self._deck.draw_cards(num_add)
        self._hand.extend(draw_cards)

        self._max_energy += 1
        self._energy = self._max_energy

    def is_alive(self) -> bool:
        """Checks if the hero is alive and still has cards in their deck."""
        return super().is_alive() and self._deck.remaining_count() > 0


# Task 8
class Minion(Card, Entity):
    """Base class for permanent cards that can act as minions on the field."""
    def __init__(self, health: int, shield: int) -> None:
        """Initializes a minion with health and shield values."""
        Card.__init__(self)
        Entity.__init__(self, health, shield)
        self._name = MINION_NAME
        self._description = MINION_DESC
        self._symbol = MINION_SYMBOL
        self._cost = 2
        self._permanent = True

    def __str__(self) -> str:
        return f"{self._name}: {self._description}"

    def __repr__(self) -> str:
        return f"Minion({self._health}, {self._shield})"

    def choose_target(self,
                      ally_hero: Entity,
                      enemy_hero: Entity,
                      ally_minions: list[Entity],
                      enemy_minions: list[Entity]) -> Entity:
        """Default targeting behavior, returns itself (no action)."""
        return self


# Task 9
class Wyrm(Minion):
    """A minion that buffs the ally with the lowest health (heal + shield)."""
    def __init__(self, health: int, shield: int) -> None:
        super().__init__(health, shield)
        self._name = WYRM_NAME
        self._description = WYRM_DESC
        self._symbol = WYRM_SYMBOL
        self._cost = 2
        self._effect = {HEALTH: 1, SHIELD: 1}
        self._permanent = True

    def __repr__(self) -> str:
        return f"Wyrm({self.get_health()}, {self.get_shield()})"

    def __str__(self) -> str:
        return f"{self._name}: {self._description}"

    def choose_target(self,
                      ally_hero: Entity,
                      enemy_hero: Entity,
                      ally_minions: list[Entity],
                      enemy_minions: list[Entity]) -> Entity:
        """Chooses the ally with the lowest health, preferring the hero if tied."""
        candidates = [ally_hero] + ally_minions
        min_health = min(entity.get_health() for entity in candidates)
        lowest = [entity for entity in candidates if entity.get_health() == min_health]
        if ally_hero in lowest:
            return ally_hero
        return lowest[0]


# Task 10
class Raptor(Minion):
    """A minion that deals damage equal to its current health to the highest HP enemy."""
    def __init__(self, health: int, shield: int) -> None:
        super().__init__(health, shield)
        self._name = RAPTOR_NAME
        self._description = RAPTOR_DESC
        self._symbol = RAPTOR_SYMBOL
        self._cost = 2
        self._permanent = True
        self.effect = {DAMAGE: health}

    def __str__(self) -> str:
        return f"{self._name}: {self._description}"

    def __repr__(self) -> str:
        return f"Raptor({self.get_health()}, {self.get_shield()})"

    def get_effect(self) -> dict[str, int]:
        """Returns the current damage effect based on its health."""
        return {"damage": self.get_health()}

    def choose_target(self,
                      ally_hero: Entity,
                      enemy_hero: Entity,
                      ally_minions: list[Entity],
                      enemy_minions: list[Entity]) -> Entity:
        """Targets the enemy minion with the highest health or the enemy hero if none exist."""
        if enemy_minions:
            max_hp = max(m.get_health() for m in enemy_minions)
            for m in enemy_minions:
                if m.get_health() == max_hp:
                    return m
        return enemy_hero

    def apply_health(self, health: int):
        """Heals the Raptor and updates its damage effect accordingly."""
        self._health += health
        self._effect = {DAMAGE: self._health}

    def apply_damage(self, damage: int):
        """Applies damage and updates damage effect based on new health."""
        if damage <= self._shield:
            self._shield -= damage
        else:
            remaining = damage - self._shield
            self._shield = 0
            self._health = max(0, self._health - remaining)
            self.effect = {DAMAGE: self._health}
        return self


def card_effect(card, target, minions):
Instructor
| 06/08 at 4:01 pm
Grading comment:
This could be a private method within HearthModel (because that's where you use it). 

    """Applies a card's effect (heal, shield, or damage) to the target entity."""
    for type, value in card.get_effect().items():
        if type == SHIELD:
            target.apply_shield(value)
        elif type == HEALTH:
            target.apply_health(value)
        elif type == DAMAGE:
            target.apply_damage(value)
    if isinstance(target, Minion) and not target.is_alive():
        if target in minions:
            minions.remove(target)


# Task 11
class HearthModel:
    """Represents the current game state including players and minions."""

    def __init__(self,
                 player: Hero,
                 active_player_minions: list[Minion],
                 enemy: Hero,
                 active_enemy_minions: list[Minion]) -> None:
        """Initializes the HearthModel with both players and their active minions."""
        self._player = player
        self._active_player_minions = active_player_minions
        self._enemy = enemy
        self._active_enemy_minions = active_enemy_minions

    def __str__(self) -> str:
        p_str = str(self._player)
        pm = ";".join(f"{m.get_symbol()},{m.get_health()},{m.get_shield()}" for m in self._active_player_minions)
Instructor
| 06/08 at 3:22 pm
Grading comment:
Variable names are not descriptive. Are we talking about the Prime Minister or a variable? Try to avoid short variable (1-2 characters) names as it's easy to get what they mean.

        e_str = str(self._enemy)
        em = ";".join(f"{m.get_symbol()},{m.get_health()},{m.get_shield()}" for m in self._active_enemy_minions)
Instructor
| 06/08 at 3:22 pm
Grading comment:
Same here

        return f"{p_str}|{pm}|{e_str}|{em}"

    def __repr__(self) -> str:
        return (
            f"HearthModel({repr(self._player)}, "
            f"{repr(self._active_player_minions)}, "
            f"{repr(self._enemy)}, "
            f"{repr(self._active_enemy_minions)})"
        )

    def get_player(self) -> Hero:
        """Returns the player hero object."""
        return self._player

    def get_enemy(self) -> Hero:
        """Returns the enemy hero object."""
        return self._enemy

    def get_player_minions(self) -> list[Minion]:
        """Returns the list of active player minions."""
        return self._active_player_minions

    def get_enemy_minions(self) -> list[Minion]:
        """Returns the list of active enemy minions."""
        return self._active_enemy_minions

    def has_won(self) -> bool:
        """Returns True if the player has won the game."""
        return self._player.is_alive() and not self._enemy.is_alive()

    def has_lost(self) -> bool:
        """Returns True if the player has lost the game."""
        return not self._player.is_alive()

    def play_card(self, card: Card, target: Entity) -> bool:
        """Handles the action of the player playing a card."""
        if self._player.spend_energy(card.get_cost()):
            self._player.get_hand().remove(card)
            if isinstance(card, Minion):
                if len(self.get_player_minions()) == MAX_MINIONS:
                    self._active_player_minions.pop(0)
                self._active_player_minions.append(card)
                return True
            else:
                card_effect(card, target, self._active_enemy_minions)
                self._active_enemy_minions = [m for m in self._active_enemy_minions if m.is_alive()]
                self._active_player_minions = [m for m in self._active_player_minions if m.is_alive()]
            return True
        return False

    def discard_card(self, card: Card) -> None:
        """Removes a card from the player's hand
         and puts it back into the deck."""
        self._player.get_hand().remove(card)
        self._player.get_deck().add_card(card)

    def end_turn(self) -> list[str]:
Instructor
| 06/08 at 4:03 pm
Grading comment:
This function could use in line comments to group and explain logical blocks. 

        """Executes end of turn logic including AI moves
         and returns names of enemy cards played."""
        played_cards_name = []
        for minion in self._active_player_minions[:]:
            target = minion.choose_target(self._player,
                                          self._enemy,
                                          self._active_player_minions,
                                          self._active_enemy_minions
                                          )
            card_effect(minion, target, self._active_enemy_minions)

            self._active_enemy_minions = [minion for minion in self._active_enemy_minions if minion.is_alive()]
            self._active_player_minions = [minion for minion in self._active_player_minions if minion.is_alive()]

        self._enemy.new_turn()
        remove_cards = []
        if self._enemy.is_alive():
Instructor
| 06/08 at 3:54 pm
Grading comment:
Everything below here is quite a long nested chain of ifs / for.
Could reduce this by making helper methods, and also for the if self._enemy.is_alive(), instead of having everything under this, use the opposite check and return []. 

            for card in self._enemy.get_hand():
                if self._enemy.spend_energy(card.get_cost()):
                    played_cards_name.append(card.get_name())
                    remove_cards.append(card)
                    if isinstance(card, Entity):
Instructor
| 06/08 at 3:28 pm
Grading comment:
This is repeated logic from the play_card logic, except it has switched enemy and player.

                        if len(self.get_enemy_minions()) == MAX_MINIONS:
                            self._active_enemy_minions.pop(0)
                        self._active_enemy_minions.append(card)
                    else:
                        for type, value in card.get_effect().items():
                            if type == SHIELD:
                                self._enemy.apply_shield(value)
                            elif type == HEALTH:
                                self._enemy.apply_health(value)
                            elif type == DAMAGE:
                                self._player.apply_damage(value)

                if not self._player.is_alive():
                    break
            for card in remove_cards:
                self._enemy.get_hand().remove(card)
            for minion in self._active_enemy_minions[:]:
                target = minion.choose_target(self._enemy,
                                              self._player,
                                              self._active_enemy_minions,
                                              self._active_player_minions
                                              )
                card_effect(minion, target, self._active_player_minions)
                self._active_enemy_minions = [minion for minion in self._active_enemy_minions if minion.is_alive()]
                self._active_player_minions = [minion for minion in self._active_player_minions if minion.is_alive()]

            self._player.new_turn()
        return played_cards_name


def create_deck(symbols):
    """Creates a deck from a list of card symbols."""
    cards = create_card(symbols)
    return CardDeck(cards)


def _process_deck_line(self, line: str) -> None:
    """Parses a single line of deck symbols and
    populates the model's player and enemy decks."""
    parts = line.split('|')
    if len(parts) >= 2:
        player_cards = parts[0].strip().split(',')
        enemy_cards = parts[1].strip().split(',')

        for card_sym in player_cards:
            if card_sym.strip():
                card = self._create_card_from_symbol(card_sym.strip())
                if card:
                    self._model.get_player().get_deck().add_card(card)

        for card_sym in enemy_cards:
            if card_sym.strip():
                card = self._create_card_from_symbol(card_sym.strip())
                if card:
                    self._model.get_enemy().get_deck().add_card(card)


def create_card(symbols):
    """Generates a list of card objects based on given symbols."""
    cards: list[Card] = []
    for symbol in symbols:
        sym = symbol.strip()
        if not sym:
            continue

        if symbol == "R":
            cards.append(Raptor(1, 0))
        elif symbol == "W":
            cards.append(Wyrm(1, 0))
        elif symbol == "H":
            cards.append(Heal())
        elif symbol == "S":
            cards.append(Shield())
        elif sym.isdigit():
            cards.append(Fireball(int(sym)))

    return cards


def create_hero(contents):
    """Creates a Hero object from a list
     of content strings specifying
      stats, deck, and hand."""
    # Foundation Values: [20, 0, 1]
    num_parts = contents[0].split(",")
    health = int(num_parts[0])
    shield = int(num_parts[1])
    max_energy = int(num_parts[2])

    # deck
    deck = create_deck(contents[1].split(","))

    # hand
    hand = create_card(contents[2].split(","))

    return Hero(health, shield, max_energy, deck, hand)


# Task 12
class HearthStone:
    """Handles user interaction,command parsing, and game loop logic."""
    PLAYER_SELECT = "M"
    ENEMY_SELECT = "O"

    def __init__(self, file: str):
        """Initializes the HearthStone game with the given file."""
        self._view = HearthView()
        self._file = file
        self._model = self.load_game(file)

    def __str__(self) -> str:
        return f"A game of HearthStone using: {self._file}"

    def __repr__(self) -> str:
        return f"Hearthstone({self._file})"

    def update_display(self, message: list[str]):
        """Updates the display with current game state and a message."""
Instructor
| 06/08 at 4:09 pm
Grading comment:
needs parameters section.

        self._view.update(
            self._model.get_player(),
            self._model.get_enemy(),
            self._model.get_player_minions(),
            self._model.get_enemy_minions(),
            message
        )

    def get_command(self) -> str:
        """Retrieves and validates a command from user input."""
        while True:
            try:
                cmd = input(COMMAND_PROMPT)

            except EOFError:
                return END_TURN_COMMAND
            command = cmd.lower()

            if command in [HELP_COMMAND, END_TURN_COMMAND]:
                return command
            elif command.startswith(LOAD_COMMAND + " "):
                return command
            elif command.startswith(PLAY_COMMAND + " ") or command.startswith(DISCARD_COMMAND + " "):
                try:
                    command_parts = command.split(" ")
                    if len(command_parts) == 2:
                        index = int(command.split(" ")[1])
                        hand_len = len(self._model._player.get_hand())
Instructor
| 06/08 at 3:56 pm
Grading comment:
should be using get_player() instead of accessing model's private variable. 

                        if 1 <= index <= hand_len:
                            return command
                except ValueError:
                    pass
            self.update_display([INVALID_COMMAND])

    def _code_to_entity(self, code: str) -> "Entity":
        """Converts a target code string to the corresponding Entity object."""
        if code == self.PLAYER_SELECT:
            return self._model.get_player()
        if code == self.ENEMY_SELECT:
            return self._model.get_enemy()
        idx = int(code[1:])
        if code.startswith(self.PLAYER_SELECT):
            return self._model.get_player_minions()[idx]
        return self._model.get_enemy_minions()[idx]

    def save_game(self):
        """Saves the current game state to a file."""
        with open(SAVE_LOC, "w", encoding="utf-8") as fp:
            fp.write(str(self._model))

    def load_game(self, file: str) -> HearthModel:
        """Loads a game state from a file and returns a HearthModel object."""
        with open(file, "r", encoding="utf-8") as fp:
            lines = fp.readlines()

        hero_line = lines[0].strip()
        p_str, pm_str, e_str, em_str = [part.strip() for part in hero_line.split("|")]

        player = create_hero(p_str.split(";"))
        enemy = create_hero(e_str.split(";"))

        def _make_minion(sym: str, hp: int, sh: int) -> Minion:
            """
                Creates a specific Minion subclass instance based on the provided symbol.

                Parameters:
                    sym (str): The symbol representing the minion type (e.g., 'R', 'W').
                    hp (int): The health value of the minion.
                    sh (int): The shield value of the minion.

                Returns:
                    Minion: An instance of Raptor, Wyrm, or a generic Minion based on the symbol.
                """
            if sym == RAPTOR_SYMBOL:
                return Raptor(hp, sh)
            if sym == WYRM_SYMBOL:
                return Wyrm(hp, sh)
            return Minion(hp, sh)

        def _read_minion_block(block: str) -> list[Minion]:
            """
                Parses a block of text representing multiple minions and creates corresponding Minion objects.

                Parameters:
                    block (str): A semicolon-separated string where each segment is a minion's data in the form 'symbol,hp,shield'.

                Returns:
                    list[Minion]: A list of instantiated Minion objects with specified properties.
                """
            out: list[Minion] = []
            if not block:
                return out
            for chunk in block.split(";"):
                parts = chunk.split(",")
                sym = parts[0].strip()
                hp = int(parts[1]) if len(parts) > 1 else 1
                sh = int(parts[2]) if len(parts) > 2 else 0
                if sym:
                    out.append(_make_minion(sym, hp, sh))
            return out

        player_minion = _read_minion_block(pm_str)
        enemy_minion = _read_minion_block(em_str)

        new_model = HearthModel(player, player_minion, enemy, enemy_minion)

        if len(lines) > 1:
            for i in range(1, len(lines)):
                line = lines[i].strip()
                if line:
                    parts = line.split('|')
                    if len(parts) >= 2:
                        player_cards = parts[0].strip()
                        if player_cards:
                            for symbol in player_cards:
                                card = create_card(symbol)
                                player.get_deck().add_card(card)

                        enemy_cards = parts[1].strip()
                        if enemy_cards:
                            for symbol in enemy_cards:
                                card = create_card(symbol)
                                enemy.get_deck().add_card(card)

        self._model = new_model
        self._file = file
        return new_model

    def check_game_end(self, message):
        """Checks if the game has ended
         and updates the message list accordingly."""
        if self._model.has_won():
            message.append(WIN_MESSAGE)
            self.update_display(message)
            return True

        if self._model.has_lost():
            message.append(LOSS_MESSAGE)
            self.update_display(message)
            return True

        return False

    def get_target_entity(self) -> str:
        """Prompts the user to select a target entity."""
        while True:
            raw = input(ENTITY_PROMPT).upper()
            code = None

            if raw == self.PLAYER_SELECT:
                code = self.PLAYER_SELECT
            elif raw == self.ENEMY_SELECT:
                code = self.ENEMY_SELECT
            elif raw.isdigit():
                n = int(raw)
                if 1 <= n <= len(self._model.get_enemy_minions()):
                    code = f"{self.ENEMY_SELECT}{n - 1}"
                elif 6 <= n <= 5 + len(self._model.get_player_minions()):
                    code = f"{self.PLAYER_SELECT}{n - 6}"
            elif (raw.startswith(self.PLAYER_SELECT) or raw.startswith(self.ENEMY_SELECT)) \
                    and raw[1:].isdigit():
                code = raw

            if code is not None:
                return code
            else:
                self.update_display([INVALID_ENTITY])

    def play(self):
        """Main game loop that processes user commands
         and updates the game state."""

        self.update_display([WELCOME_MESSAGE])

        while True:
            message = []
            command = self.get_command()

            # help command
            if command == HELP_COMMAND:
                message.extend(HELP_MESSAGES)

            # load game
            if command.startswith(LOAD_COMMAND + " "):
                fname = command.split(" ", 1)[1]
                try:
                    self._model = self.load_game(fname)
                    message.append(GAME_LOAD_MESSAGE + fname)
                except FileNotFoundError:
                    message.append(f"{fname}{NO_FILE_MESSAGE}")
                except (ValueError, IndexError) as err:
                    message.append(f"Malformed File: {err}")
                self.update_display(message)
                continue

            # play card
            if command.startswith(PLAY_COMMAND + " "):
                try:
                    idx = int(command.split()[1]) - 1
                except (IndexError, ValueError):
                    message.append(INVALID_COMMAND)
                else:
                    hand = self._model.get_player().get_hand()
                    player = self._model.get_player()

                    if not (0 <= idx < len(hand)):
                        message.append(INVALID_COMMAND)
                    else:
                        card_to_play = hand[idx]

                        if (not card_to_play.is_permanent()
                                and card_to_play.get_effect()):
                            code = self.get_target_entity()
                            target = self._code_to_entity(code)
                        else:
                            target = None

                        if card_to_play.get_cost() > player.get_energy():
                            message.append(ENERGY_MESSAGE)
                            self.update_display(message)
                            continue

                        self._model.play_card(card_to_play, target)
                        message.append(PLAY_MESSAGE + card_to_play.get_name())

                        if self.check_game_end(message):
                            break

            if command.startswith(DISCARD_COMMAND + " "):
                try:
                    idx = int(command.split()[1]) - 1
                except (IndexError, ValueError):
                    message.append(INVALID_COMMAND)
                    self.update_display(message)
                    continue

                hand = self._model.get_player().get_hand()

                if 0 <= idx < len(hand):
                    card_to_discard = hand[idx]
                    self._model.discard_card(card_to_discard)
                    message.append(DISCARD_MESSAGE + card_to_discard.get_name())
                else:
                    message.append(INVALID_COMMAND)

            if command.startswith(END_TURN_COMMAND):
                enemy_cards = self._model.end_turn()
                for card in enemy_cards:
                    message.append(ENEMY_PLAY_MESSAGE + card)

                if self.check_game_end(message):
                    break

                self.save_game()
                message.append(GAME_SAVE_MESSAGE)

            self.update_display(message)


# Task 13
def play_game(file: str):
    """Attempts to start the game using the given file,
        falls back to autosave if loading fails."""
    try:
        controller = HearthStone(file)
    except (ValueError, FileNotFoundError):
        controller = HearthStone(SAVE_LOC)
    controller.play()


# Task 14
def main():
    """Main function to start the game
    with a default level file."""
    game = HearthStone("levels/deck1.txt")
    game.play()


if __name__ == "__main__":
    main()
