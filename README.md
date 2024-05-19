## adding inputs

```nix
# flake.nix
                                                                                
{
  inputs = {
    hyprland.url = "git+https://github.com/hyprwm/Hyprland?submodules=1";
    # ...
  };
  # ...
}
```

## setting hyprland package

```nix
# configuration.nix

{ pkgs, lib, inputs, ... }: {

  programs.hyprland.enable = true;
  programs.hyprland.package = inputs.hyprland.packages."${pkgs.system}".hyprland;

}
```

## inline scripts

```nix
# home.nix

{ pkgs, lib, config, ... }:

  let
    startupScript = pkgs.pkgs.writeShellScriptBin "start" ''
      ${pkgs.waybar}/bin/waybar &
      ${pkgs.swww}/bin/swww init &
  
      sleep 1
  
      ${pkgs.swww}/bin/swww img ${./wallpaper.png} &
    '';
in
{
  wayland.windowManager.hyprland = {
    enable = true;

    settings = {
      exec-once = ''${startupScript}/bin/start'';
    };
  };
}
```

## adding plugin

```nix
# home.nix

{ pkgs, lib, inputs, ... }: 
                                                                          
{
  wayland.windowManager.hyprland = {

    enable = true;

    plugins = [
      inputs.hyprland-plugins.packages."${pkgs.system}".borders-plus-plus
    ];

    settings = {
      "plugin:borders-plus-plus" = {
        add_borders = 1; # 0 - 9

        # you can add up to 9 borders
        "col.border_1" = "rgb(ffffff)";
        "col.border_2" = "rgb(2222ff)";

        # -1 means "default" as in the one defined in general:border_size
        border_size_1 = 10;
        border_size_2 = -1;

        # makes outer edges match rounding of the parent. Turn on / off to better understand. Default = on.
        natural_rounding = "yes";
      };
    };
  };
}
```
