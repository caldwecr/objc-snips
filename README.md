# objc-snips
Useful snippets for obj-c organized in a README

# .h
```obj-c
@property (atomic, strong)NSString *roadName;
@property (nonatomic, assign)BOOL paused;
```

# Physics
https://www.makeschool.com/tutorials/learn-to-build-flappy-bird

## Configuring the physics body

```obj-c
- (void)didLoadFromCCB {
    _topPipe.physicsBody.collisionType = @"level";
    _topPipe.physicsBody.sensor = TRUE;
    _bottomPipe.physicsBody.collisionType = @"level";
    _bottomPipe.physicsBody.sensor = TRUE;
}
```

## Applying impulses
```obj-c
[_hero.physicsBody applyImpulse:ccp(0, 400.f)];
[_hero.physicsBody applyAngularImpulse:10000.f];
```

## Limiting velocity
```obj-c
float yVelocity = clampf(_hero.physicsBody.velocity.y, -1 * MAXFLOAT, 200.f);
_hero.physicsBody.velocity = ccp(0, yVelocity);
```

## Limiting angular velocity
```obj-c
_sinceTouch += delta;
    _hero.rotation = clampf(_hero.rotation, -30.f, 90.f);
    if (_hero.physicsBody.allowsRotation) {
        float angularVelocity = clampf(_hero.physicsBody.angularVelocity, -2.f, 1.f);
        _hero.physicsBody.angularVelocity = angularVelocity;
    }
    if ((_sinceTouch > 0.5f)) {
        [_hero.physicsBody applyAngularImpulse:-40000.f*delta];
    }
```

# Cocos node stuff

## Add nodes programmatically
```obj-c
- (void)spawnNewObstacle {
    CCNode *previousObstacle = [_obstacles lastObject];
    CGFloat previousObstacleXPosition = previousObstacle.position.x;
    if (!previousObstacle) {
        // this is the first obstacle
        previousObstacleXPosition = firstObstaclePosition;
    }
    CCNode *obstacle = [CCBReader load:@"Obstacle"];
    obstacle.position = ccp(previousObstacleXPosition + distanceBetweenObstacles, 0);
    [_physicsNode addChild:obstacle];
    [_obstacles addObject:obstacle];
}
```

# Audio stuff

_note:_ the audio files will appear in xcode under Resources/Published-iOS/ but when you need to specify the file name of the file you should describe the path to the file relative to Resources/Published-iOS. As an example if you had an audio file at Resources/Published-iOS/audio/song_1.m4a then you would provide @"audio/song_1.m4a" to playEffect.

http://kstenerud.github.io/ObjectAL-for-iPhone/documentation/index.html
```obj-c
@implementation OALSimpleAudioSample
- (id) init
{
    if(nil != (self = [super init]))
    {
        // We don't want ipod music to keep playing since
        // we have our own bg music.
        [OALSimpleAudio sharedInstance].allowIpod = NO;
        
        // Mute all audio if the silent switch is turned on.
        [OALSimpleAudio sharedInstance].honorSilentSwitch = YES;
        
        // This loads the sound effects into memory so that
        // there's no delay when we tell it to play them.
        [[OALSimpleAudio sharedInstance] preloadEffect:SHOOT_SOUND];
        [[OALSimpleAudio sharedInstance] preloadEffect:EXPLODE_SOUND];
    }
    return self;
}
- (void) onGameStart
{
    // Play the BG music and loop it.
    [[OALSimpleAudio sharedInstance] playBg:INGAME_MUSIC_FILE loop:YES];
}
- (void) onGamePause
{
    [OALSimpleAudio sharedInstance].paused = YES;
}
- (void) onGameResume
{
    [OALSimpleAudio sharedInstance].paused = NO;
}
- (void) onGameOver
{
    // Could use stopEverything here if you want
    [[OALSimpleAudio sharedInstance] stopAllEffects];
    
    // We only play the game over music through once.
    [[OALSimpleAudio sharedInstance] playBg:GAMEOVER_MUSIC_FILE];
}
- (void) onShipShotABullet
{
    [[OALSimpleAudio sharedInstance] playEffect:SHOOT_SOUND];
}
- (void) onShipGotHit
{
    [[OALSimpleAudio sharedInstance] playEffect:EXPLODE_SOUND];
}
- (void) onQuitToMainMenu
{
    // Stop all music and sound effects.
    [[OALSimpleAudio sharedInstance] stopEverything];   
    
    // Unload all sound effects and bg music so that it doesn't fill
    // memory unnecessarily.
    [[OALSimpleAudio sharedInstance] unloadAllEffects];
}
@end
```

# Generating random numbers

## Integer
```obj-c
- (int)generateNumber:(int)max {
    int r = arc4random_uniform(max);
    return r;
}
```

# Meta-programming stuff

[Dynamically calling methods](http://stackoverflow.com/questions/4446883/objective-c-calling-method-dynamically-with-a-string)

```obj-c
SEL s = NSSelectorFromString(selectorName);
[anObject performSelector:s];
```

```obj-c
NSString *message = [self getSomeSelectorName];
objc_msgSend(self, message);

NSString *message = [self getSomeSelectorNameWithManyArguments];
objc_msgSend(self, message, arg1, arg2, arg3, arg4);
```
