work in progress
```
;*
-
- 										Startups
- *****************************************************************************************
*;

(script startup void game_start
	; Visual startups
    (fade_in 1.0 1.0 1.0 15)
	(set render_postprocess_saturation 1.0)
	(chud_show_crosshair false)

	

    (sleep 30)

	; Gameplay stuff
	(checkStats)
	(wake clearPlayerInput) ; Clean up input that might persist through a map reset or if the player is holding down a button for whatever reason
)

;*
-
- 										Visual stuff
- *****************************************************************************************
*;

(script static void change_hue (real value)
    (set render_postprocess_hue (+ render_postprocess_hue value))
)

(script static void reset_hue
	(set render_postprocess_hue 0)
)

;*
-
- 										Trigger volumes
- *****************************************************************************************
*;

(script continuous void dummyTest
	(sleep_until (volume_test_players "spartan_tv") 1)
	(get_dialog 1 false true)
	(sleep 70)
	(sleep_until (volume_test_players "spartan_tv") 1)
	(get_dialog 2 false true)
	(sleep 70)
	(sleep_until (volume_test_players "spartan_tv") 1)
	(get_dialog 3 false true)
	(sleep 700)
	(sleep_until (volume_test_players "spartan_tv") 1)
	(get_dialog 4 false true)
	(sleep_forever)
)

(script continuous void node13
	(sleep_until (volume_test_players "tv_13") 1)
	(write "[Node 13] : Hit (Reset hue)")
	(sleep 5)
	(reset_hue)
	(sleep 200)
)

(script continuous void node14
    (sleep_until (volume_test_players "tv_14") 1)
    (write "[Node 14] : Hit (+ 5.0 hue)")
    (sleep 5)
    (change_hue 5.0)
)

(script continuous void tv_end
    (sleep_until (volume_test_players "tv_progression_5") 1)
    (wake end)
)

;*
-
- 										Gameplay
- *****************************************************************************************
*;

(global boolean coop false)
(global short playerCount 0)

(script dormant void end
    (fade_out 1.0 1.0 1.0 50)
    (sleep 50)
    ;*explode, yay*;
	(game_won)
    (sleep_forever)
)

(script dormant void clearPlayerInput
	(begin 
		(sleep (* 30.0 60.0 0.02))
		(player_action_test_reset)
	)

)

(script static void checkStats
	(begin 
		(if (= (game_coop_player_count ) 1)
			(set coop false)
			(set playerCount 1)
		)
		(if (= (game_coop_player_count) 2)
			(set coop true)
			(set playerCount 2)
		)
		(if (>= (game_coop_player_count) 3)
			(set coop true)
			(set playerCount 4)
		)
	)
)


;*
-
- 										Dialog system
- *****************************************************************************************
*;

; Dialog index tracker
(global long lastIndex 0)

; Function so I don't have to write 'chud_post_message_hack' everytime I want to print something to the killfeed
(script static void write (string message)
    (chud_post_message_hack message)
)

; For those slow readers (the killfeed is super fucking fast)
(script continuous void get_last_dialog
	(if (and (player_action_test_dpad_left) (>= lastIndex 1))
		(begin
			(get_dialog lastIndex false true)
			(write "(repeating...)")
			(sleep 50)
			(player_action_test_reset)
		)
	)
)

; Dialog index
(script static void get_dialog (long arrayNumber, boolean isCoop, boolean isFemale) 
	(begin
		(if (= arrayNumber 0)
			(sleep 1))  ;* Nothing *;

		(if (= arrayNumber 1)
			(begin
				(if (and (= coop true) (= playerCount 2))
					(write "Oh... Hello! I see you brought a friend"))
				(if (and (= coop true) (> playerCount 23))
					(write "Oh... Hello! I see you brought a friends"))
				(if (= coop false)
					(write "Didn't I tell you to bring a friend?"))))

		(if (= arrayNumber 2)
			(write "Welcome to my test map"))

		(if (= arrayNumber 3)
			(write "Walk up to the nodes behind me to trigger different scripts!"))

		(if (= arrayNumber 4)
			(write "So uh, get going..."))

		(if (= arrayNumber 5)
			(write "Bro is getting nothing done"))


	(set lastIndex arrayNumber)
	)
)
```
