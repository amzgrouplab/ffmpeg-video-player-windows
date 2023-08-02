4K video player

A portable video player based on ffmpeg for windows platform.

It is a simple and light weight player implemention without SDL or other third party library. player core codes are written in C language, KISS and easy to read.

It directly access to video and audio rendering device on specific platforms. on win32 playform, using gdi & direct3d api implements video rendering

Currently, this player can smoothly playback many video files, with low cpu and memory usage (compared with ffplay of offical ffmpeg), high performance, compatibility and stability, and the audio/video also synchronized very well.


testplayer
==========
testplayer is a simple test player for fanplayer  
hot-keys for testplayer:  
ctrl+1    - open file with single video player mode 
ctrl+2    - open files with reat video player mode 
ctrl+F    - step seek forward  
ctrl+B    - step seek backward  
ctrl+right- volume up
ctrl+left - volume down

To adjust the play position of the video, we can simply click on the bottom of the progress bar in the video player.



src/ffplayer.h

// function declaration
void* player_open      (char *file, void *appdata, PLAYER_INIT_PARAMS *params);
void* playBackwordVideo(char* rearvideo_path, char* frontvideo_path, void* appdata, PLAYER_INIT_PARAMS* params);
void  player_close     (void *hplayer);
void  player_play      (void *hplayer);
void  player_pause     (void *hplayer);
void  player_seek      (void *hplayer, int64_t ms, int type);
void  player_setrect   (void *hplayer, int type, int x, int y, int w, int h); // type: 0 - video rect, 1 - visual effect rect
int   player_snapshot  (void *hplayer, char *file, int w, int h, int waitt);
int   player_record    (void *hplayer, char *file);
void  player_setparam  (void *hplayer, int id, void *param);
void  player_getparam  (void *hplayer, int id, void *param);

// internal helper function
void  player_send_message(void *extra, int32_t msg, void *param);
void  player_load_params (PLAYER_INIT_PARAMS *params, char *str);

// function description
/*
player_open creates a single video player object
     file - file path
     appdata - The win32 platform passes in the window handle
     params - player initialization parameters
     Return value - void* pointer type, pointing to the player object
playBackwordVideo creates a rear video player object
    *rear video and front video must be recorded at the same time. but the end time can be different*
    rearvideo_path - rear video path
    frontvideo_path - front video path
    win - The win32 platform passes in the window handle
    params - player initialization parameters
    Return value - void* pointer type, pointing to the player object

player_close closes the player
     hplayer - Pointer to the player object returned by player_open

player_play start playing
     hplayer - Pointer to the player object returned by player_open

player_pause pause playback
     hplayer - Pointer to the player object returned by player_open

player_seek Jump to the specified position
     hplayer - Pointer to the player object returned by player_open
     ms - the specified position in milliseconds
     type - the specified type, 0 / SEEK_STEP_FORWARD / SEEK_STEP_BACKWARD
     If type is 0, it is a normal seek operation, and ms specifies the seek position, in milliseconds
     If type is SEEK_STEP_FORWARD, it will step forward 10 seconds
     If type is SEEK_STEP_FORWARD, it will step backward 10 seconds

player_serect sets the display area, there are two display areas, video display area and visual effect display area
     hplayer - Pointer to the player object returned by player_open
     type - specifies the area type 0 - video rect, 1 - visual effect rect
     x,y,w,h - specify the display area


player_setparam set parameters
     hplayer - Pointer to the player object returned by player_open
     id - the parameter id
     param - the parameter pointer

player_getparam get parameters
     hplayer - Pointer to the player object returned by player_open
     id - the parameter id
     param - the parameter pointer
  */

  // Dynamic parameter description
  /*
  PARAM_MEDIA_DURATION and PARAM_MEDIA_POSITION
  Used to get the total length and current playback position of the multimedia file (in milliseconds)
  LONGLONG total = 1, pos = 0;
  player_getparam(g_hplayer, PARAM_MEDIA_DURATION, &total);
  player_getparam(g_hplayer, PARAM_MEDIA_POSITION, &pos );

  PARAM_VIDEO_WIDTH and PARAM_VIDEO_HEIGHT
  Used to get the video width and height of the multimedia file (in pixels)
  int vw = 0, vh = 0;
  player_getparam(g_hplayer, PARAM_VIDEO_WIDTH , &vw);
  player_getparam(g_hplayer, PARAM_VIDEO_HEIGHT, &vh);

  PARAM_AUDIO_VOLUME
  Used to set the playback volume, different from the system volume, fanplayer has a software volume control unit from -30dB to +12dB inside
  Volume range: [-182, 73], -182 corresponds to -30dB, 73 corresponds to +12dB
  Special values: 0 for 0dB gain, -255 for mute, +255 for maximum gain
  int volume = -0;
  player_setparam(g_hplayer, PARAM_AUDIO_VOLUME, &volume);

  PARAM_AVSYNC_TIME_DIFF
  Used to set the time synchronization difference between audio and video (in milliseconds)
  int diff = 100;
  player_setparam(g_hplayer, PARAM_AVSYNC_TIME_DIFF, &diff);
  With a setting of 100 the audio will be 100ms faster than the video, with a setting of -100 it will be 100ms slower

  PARAM_PLAYER_INIT_PARAMS
  Used to obtain player initialization parameters, using method:
  PLAYER_INIT_PARAMS params;
  player_getparam(g_hplayer, PARAM_PLAYER_INIT_PARAMS, &params);
  For the meaning of the parameters, please refer to the description of the player initialization parameters

  All parameters can be get, but not all parameters can be set, because some parameters are read-only.
  */

