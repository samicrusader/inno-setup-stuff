# VCL Styles (customizing the look and feel of your Inno Setup installer)

Developers at [The Road to Delphi](https://theroadtodelphi.com/2013/12/11/vcl-styles-for-inno-setup/) ported their [VCL Styles](https://github.com/RRUZ/vcl-styles-utils) project to Inno Setup. We can utilize this to skin our installers.

Extract [VCLStylesInno.7z](VCLStylesInno.7z) somewhere on your computer. This is a portable repack of the Inno skinning library. It also includes some tools for testing out and working on VCL styles.

From the VCLStylesInno folder, copy `VclStylesinno.dll` to your project directory, along with a style of your choosing. Use the following code, replacing Glow.vsf with your chosen skin.

```pascal
[Files]
Source: "VclStylesinno.dll"; Flags: dontcopy
Source: "Glow.vsf"; Flags: dontcopy

[Code]
procedure LoadVCLStyle(VClStyleFile: String); external 'LoadVCLStyleW@files:VclStylesInno.dll stdcall';
procedure UnLoadVCLStyles; external 'UnLoadVCLStyles@files:VclStylesInno.dll stdcall';

function InitializeSetup(): Boolean;
begin
  ExtractTemporaryFile('Glow.vsf');
  LoadVCLStyle(ExpandConstant('{tmp}\Glow.vsf'));
end;
```
With Inno Setup 6.x, there's a new bug with the previous/next/exit buttons being misaligned. This is a quick fix however:
```pascal
<event('InitializeWizard')>
procedure InitializeWizardVCLStylesFix();
begin
  WizardForm.NextButton.Left := WizardForm.BackButton.Left + WizardForm.BackButton.Width + ScaleX(2);
  WizardForm.CancelButton.Left := WizardForm.NextButton.Left + WizardForm.NextButton.Width + ScaleX(3);
  WizardForm.CancelButton.Top := WizardForm.NextButton.Top;
end;
```
