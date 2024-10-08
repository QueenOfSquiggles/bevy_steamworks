# bevy-steamworks

[![Main Branch CI Checks](https://github.com/QueenOfSquiggles/bevy_steamworks/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/QueenOfSquiggles/bevy_steamworks/actions/workflows/ci.yml)

<!-- 
These Badges are from original repo, they're not really mine to use
[![crates.io](https://img.shields.io/crates/v/bevy-steamworks.svg)](https://crates.io/crates/bevy-steamworks)
[![Documentation](https://docs.rs/bevy-steamworks/badge.svg)](https://docs.rs/bevy-steamworks)
![License](https://img.shields.io/crates/l/bevy-steamworks.svg) -->

This crate provides a [Bevy](https://bevyengine.org/) plugin for integrating with
the Steamworks SDK.

## Installation
Add the following to your `Cargo.toml`:

```toml
[dependencies]
bevy-steamworks = { git = "https://github.com/QueenOfSquiggles/bevy_steamworks.git" }
```

optionally you can add `rev="<commit>"` or `rev="<tag>"` to specify a specific version of the repo without worry of sudden changes. Though the goal is to keep the repo's main branch relatively stable with minimal API changes.

The steamworks crate comes bundled with the redistributable dynamic libraries
of a compatible version of the SDK. Currently it's v158a.

### Enabling Serde support for Steamworks
If you wish to enable serde support add the following:

```toml
[dependencies]
bevy-steamworks = { git = "https://github.com/QueenOfSquiggles/bevy_steamworks.git", features = ["serde"] }
```

### Enabling development features

Some additional features are available for ease of development, notably `SteamworksPlugin::init_dev` which initializes a version of the plugin using the Spacewar App ID which is commonly used for SDK testing since it's an example game by Valve.

```toml
[dependencies]
bevy-steamworks = { git = "https://github.com/QueenOfSquiggles/bevy_steamworks.git", features = ["dev"] }

```

## Usage

To add the plugin to your app, simply add the `SteamworksPlugin` to your
`App`. This will require the `AppId` provided to you by Valve for initialization.

```rust no_run
use bevy::prelude::*;
use bevy_steamworks::*;

fn main() {
  // Use the demo Steam AppId for SpaceWar
  App::new()
      // it is important to add the plugin before `RenderPlugin` that comes with `DefaultPlugins`
      .add_plugins(SteamworksPlugin::init_app(480).unwrap())
      .add_plugins(DefaultPlugins)
      .run()
}
```

The plugin adds `Client` as a Bevy ECS resource, which can be
accessed like any other resource in Bevy. The client implements `Send` and `Sync`
and can be used to make requests via the SDK from any of Bevy's threads.

The plugin will automatically call `SingleClient::run_callbacks` on the Bevy
every tick in the `First` schedule, so there is no need to run it manually.

All callbacks are forwarded as `Events` and can be listened to in the a
Bevy idiomatic way:

```rust no_run
use bevy::prelude::*;
use bevy_steamworks::*;

fn steam_system(steam_client: Res<Client>) {
  for friend in steam_client.friends().get_friends(FriendFlags::IMMEDIATE) {
    println!("Friend: {:?} - {}({:?})", friend.id(), friend.name(), friend.state());
  }
}

fn main() {
  // Use the demo Steam AppId for SpaceWar
  App::new()
      // it is important to add the plugin before `RenderPlugin` that comes with `DefaultPlugins`
      .add_plugins(SteamworksPlugin::init_app(480).unwrap())
      .add_plugins(DefaultPlugins)
      .add_systems(Startup, steam_system)
      .run()
}
```

## Bevy Version Supported
 
|Bevy Version |bevy\_steamworks|
|:------------|:---------------|
|0.14.\*         |  main, tag 0.1.\* |
