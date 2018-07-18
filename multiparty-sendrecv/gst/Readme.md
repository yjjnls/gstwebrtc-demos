1.  [MP] connect to signal bridge, send `HELLO id`
2.  [S] response `HELLO`
3.  [MP] `do_registration` -> `join_room_on_server` send `ROOM room_id`
4.  [S] response `ROOM_OK peer_ids` and send other peers `ROOM_PEER_JOINED id`
5.  [MP] `do_join_room` -> `start_pipeline`  
                            audiotestsrc ! queue ! opusenc ! rtpopuspay ! queue ! " RTP_CAPS_OPUS(96) " ! tee name=audiotee ! queue ! fakesink
                        -> for every peer: `add_peer_to_pipeline` (offer)  --> generate local offer sdp and ice
                                            tee name=audiotee ! queue ! webrtcbin
                                            ->`on_incoming_decodebin_stream`  webrtcbin->decodebin->autovideosink/autoaudiosink
6.  [MP] others received `ROOM_PEER_JOINED id`, store the id of the newly joined peer
7.  [MP] for every peer: on_offer_created/send_ice_candidate_message --> send offer and ice  `ROOM_PEER_MSG peer_id msg`
8.  [S] transfer msg to other peer
9.  [MP] others `handle_peer_message`->`incoming_call_from_peer` ->`add_peer_to_pipeline` (answer)
                                     ->`handle_sdp_offer`