# webrtc-sendrecv analysis

1.  启动程序连接signal bridge，消息接口on_server_message
2.  接收到HELLO消息，setup_call与对端连接
3.  接收到SESSION_OK消息，start_pipeline，创建webrtc pipeline
    包括create offer、set local description、send offer 和send ice candidate。
    offer sdp会根据pipeline中的caps来自动生成。
4.  接收到answer sdp消息，解析并set remote description
5.  接收到candidate，set remote candidate

```c
// application 
gst_element_set_state (GST_ELEMENT (pipe1), GST_STATE_PLAYING);

// gstwebrtcbin.c
gst_webrtc_bin_change_state() NULL => READY
_update_need_negotiation()
_check_need_negotiation_task()
g_signal_emit (webrtc, gst_webrtc_bin_signals[ON_NEGOTIATION_NEEDED_SIGNAL], 0);

// application
on_negotiation_needed() <--- ON_NEGOTIATION_NEEDED_SIGNAL
g_signal_emit_by_name (webrtc1, "create-offer", NULL, promise);

// gstwebrtcbin.c
gst_webrtc_bin_create_offer() <--- "create-offer"
_create_sdp_task()
_create_offer_task()
sdp_media_from_transceiver()
_create_sdp_task()::gst_promise_reply (data->promise, s);

// application
on_offer_created() <--- gst_promise_new_with_change_func
g_signal_emit_by_name (webrtc1, "set-local-description", offer, promise);
send_sdp_offer()

// gstwebrtcbin.c
gst_webrtc_bin_set_local_description() <--- set-local-description
_set_description_task()

gst_webrtc_bin_change_state()::change_state() READY => PAUSED
gst_webrtc_bin_change_state()::change_state() PAUSED => PLAYING


```