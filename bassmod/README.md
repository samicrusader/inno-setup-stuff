# BASS (Music in Inno Setup)

The BASS module works well in Pascal, allowing for your installer to play music. This code plays a file called `music.ogg`.

```pascal
[Files]
Source: "bass.dll"; Flags: dontcopy
Source: "music.ogg"; Flags: dontcopy

[Code]
const
    BASS_SAMPLE_LOOP = 4;
    BASS_UNICODE = $80000000;
    BASS_CONFIG_GVOL_STREAM = 5;
    EncodingFlag = BASS_UNICODE;
type
    HSTREAM = DWORD;

function BASS_Init(device: LongInt; freq, flags: DWORD; 
    win: HWND; clsid: Cardinal): BOOL;
    external 'BASS_Init@files:bass.dll stdcall';
function BASS_StreamCreateFile(mem: BOOL; f: string; offset1: DWORD; 
    offset2: DWORD; length1: DWORD; length2: DWORD; flags: DWORD): HSTREAM;
    external 'BASS_StreamCreateFile@files:bass.dll stdcall';
function BASS_ChannelPlay(handle: DWORD; restart: BOOL): BOOL; 
    external 'BASS_ChannelPlay@files:bass.dll stdcall';
function BASS_SetConfig(option: DWORD; value: DWORD ): BOOL;
    external 'BASS_SetConfig@files:bass.dll stdcall';
function BASS_Free: BOOL;
    external 'BASS_Free@files:bass.dll stdcall';

procedure InitializeWizard();
var
    StreamHandle: HSTREAM;
begin
    ExtractTemporaryFile('music.ogg');
    if BASS_Init(-1, 44100, 0, 0, 0) then
    begin
        StreamHandle := BASS_StreamCreateFile(False, ExpandConstant('{tmp}\music.ogg'), 0, 0, 0, 0, EncodingFlag or BASS_SAMPLE_LOOP);
        BASS_SetConfig(BASS_CONFIG_GVOL_STREAM, 2500);
        BASS_ChannelPlay(StreamHandle, False);
    end;
end;

procedure DeinitializeSetup();
begin
    BASS_Free;
end;
```
