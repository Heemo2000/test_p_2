---
title: Side Scrolling Shooting
seo_title: Side Scrolling Shooting
summary: A shooting platformer inspired by classics Super Mario Bros and Contra.
description: A shooting platformer inspired by classics Super Mario Bros and Contra.
slug: side-scrolling-shooting
author: Himanshu

draft: false
date: 2019-02-20T03:52:30-05:00
lastmod: 
expiryDate: 
publishDate: 

feature_image: platformer.png
feature_video: videos/platformer.mp4
feature_image_alt: Side Scrolling Shooting

project types: 
    - Personal

techstack:
    - Unity
    - GIMP
live_url: https://chungununo1.itch.io/side-scroller-shooting-game
source_url: https://github.com/Heemo2000/Side-Scrolling-Shooting-Game
---

This is the <b>platformer</b> inspired by the classics <b>Mario</b> and <b>Contra</b>. In this, player has to reach the end of the level while fighting
with the enemies on the way.

Here are some technical details of the project:

* Implemented mario style jumping mechanic.

* Implemented <b>weapon aiming</b> using animation rigging.

* Implemented various types of bullets.

* Implemented obstacles.

* Implement enemy AI via state machine.

## Code Blocks

### Code block for jump logic

```C#
private void Jump()
{
    if(IsGrounded())
    {
        playerAnimator.SetTrigger(StringHolder.JumpTriggerAnimParameter);
        _velocityY += _initialJumpVelocity;
    }
}

private void HandleMovement()
{
    CalculateParameters();
    _smoothedXInput = Mathf.Lerp(_smoothedXInput,_xInput,xInputSpeed * Time.deltaTime)
    Vector3 direction = (_aimPos - transform.position).normalized;
    
    float animMoveInput = (Mathf.Sign(direction.x) == Mathf.Sign(_smoothedXInput)) ? 1f : -1f
    animMoveInput *= Mathf.Abs(_smoothedXInput);
    
    playerAnimator.SetFloat("MoveInput",animMoveInput)
    HandleRotation();
    
    _controller.Move(Vector3.right * _smoothedXInput * moveSpeed * Time.fixedDeltaTime);
    _controller.Move(Vector3.up * _velocityY * Time.fixedDeltaTime);
    LockPositionInZ()
    HandleGravity();

private void HandleGravity()
{
    
    bool isFalling = _velocityY < 0.0f
    if(!IsGrounded())
    {
        float currentVelocityY = _velocityY;
        float currentGravity = _gravity;
        
        if(isFalling)
        {
            playerAnimator.SetBool("IsFalling",true);
            currentGravity *= fallMultiplier;
        }
        float newVelocityY = currentVelocityY + (currentGravity * Time.fixedDeltaTime);
        float nextVelocityY = (currentVelocityY + newVelocityY) * 0.5f;
        _velocityY = nextVelocityY
    }
    else
    {
        _velocityY = 0f;
    
    if(AlmostOnGround() && isFalling)
    {    
        playerAnimator.SetBool("IsFalling",false);
    }
}
```

### Code block for aiming logic
```C#
public class PlayerAim : MonoBehaviour
{
    [SerializeField]private RigBuilder rigBuilder;
    [SerializeField]private MultiAimConstraint headIK;
    [SerializeField]private MultiAimConstraint spineIK;

    [SerializeField]private MultiAimConstraint rightHandIK;
    private PlayerMovement _movement;

    GameObject _mousePointerPrimitive;
    private void Awake() 
    {
        _movement = GetComponent<PlayerMovement>();

        _mousePointerPrimitive = GameObject.CreatePrimitive(PrimitiveType.Sphere);

        _mousePointerPrimitive.GetComponent<Collider>().enabled = false;

        
        var headIKSourceObjects = headIK.data.sourceObjects;
        var spineIKSourceObjects = spineIK.data.sourceObjects;
        var rightHandIKSourceObjects = rightHandIK.data.sourceObjects;

        headIKSourceObjects.SetTransform(0,_mousePointerPrimitive.transform);
        spineIKSourceObjects.SetTransform(0,_mousePointerPrimitive.transform);
        rightHandIKSourceObjects.SetTransform(0,_mousePointerPrimitive.transform);

        headIK.data.sourceObjects = headIKSourceObjects;
        spineIK.data.sourceObjects = spineIKSourceObjects;
        rightHandIK.data.sourceObjects = rightHandIKSourceObjects;

        rigBuilder.Build();
    }


    void LateUpdate()
    {
        _mousePointerPrimitive.transform.position = _movement.MouseWorldPos;
    }
}
```