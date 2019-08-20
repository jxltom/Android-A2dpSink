Apply patches to enable Android to support the Bluetooth a2dpsink protocol, which allows Android to work like a Bluetooth speaker for audio output.
- a2dpsink_android5.1.patch for android 5.1
- a2dpsink_android8.1.patch for android 8.1
1. Apply a2dpsink_android5.1.patch or a2dpsink_android8.1.patch.
2. Recompile Android.
1. Open this Android device Settings->Bluetooth page, then open Bluetooth.
2. The mobile phone connects to this Android device just like a Bluetooth headset.
3. The phone plays music, at this time the Android device will be able to play the music in the phone.
## Get media information
At this point, you can play music. If you need to get the song information being played, you can operate as follows.
```java
public class A2dpSinkPresenter {
    private static final String TAG = "A2dpSinkPresenter";
    private static final String ACTION_TRACK_EVENT =
            "android.bluetooth.avrcp-controller.profile.action.TRACK_EVENT";
    public static final String EXTRA_METADATA =
            "android.bluetooth.avrcp-controller.profile.extra.METADATA";
    public static final String EXTRA_PLAYBACK =
            "android.bluetooth.avrcp-controller.profile.extra.PLAYBACK";

    private Context mContext;
    private IA2dpSinkView mA2dpSinkView;

    public A2dpSinkPresenter(Context context, IA2dpSinkView view) {
        mContext = context;
        mA2dpSinkView = view;
    }

    public void start() {
        IntentFilter filter = new IntentFilter();
        filter.addAction(ACTION_TRACK_EVENT);
        mContext.registerReceiver(mBtReceiver, filter);
    }

    public void stop() {
        mContext.unregisterReceiver(mBtReceiver);
    }

    private BroadcastReceiver mBtReceiver = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            Log.d(TAG, "onReceive intent=" + intent);
            String action = intent.getAction();

            if (ACTION_TRACK_EVENT.equals(action)) {
                PlaybackState pbb = intent.getParcelableExtra(EXTRA_PLAYBACK);
                MediaMetadata mmd = intent.getParcelableExtra(EXTRA_METADATA);
                if (null != mmd) {
                    mA2dpSinkView.updateA2dpSinkMediaInfo(mmd);
                }
            }
        }
    };
}