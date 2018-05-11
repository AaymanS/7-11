-----------------------------------------------------------------------------------------
-- Created by: Aayman Shameem
-- Created on: May 10, 2018
-- 
-- This code will make one character shoot and the other die, all in a physics world
-----------------------------------------------------------------------------------------

display.setStatusBar(display.HiddenStatusBar)

-- Gravity
local physics = require( "physics" )

physics.start()
physics.setGravity( 0, 25 ) -- (x, y)
physics.setDrawMode( "normal" ) -- no collision outlines

local playerBullets = {} -- table holding ALL of the player's bullets

local leftWall = display.newRect( 0, display.contentHeight / 2, 1, display.contentHeight )
-- myRectangle.strokeWidth = 3
-- myRectangle:setFillColor( 0.5 )
-- myRectangle:setStrokeColor( 1, 0, 0 )
leftWall.alpha = 0.0
physics.addBody( leftWall, "static", { 
    friction = 0.5, 
    bounce = 0.3 
    } )

local theGround1 = display.newImage( "./assets/sprites/land.png" )
theGround1.x = 520
theGround1.y = display.contentHeight
theGround1.id = "the ground"
physics.addBody( theGround1, "static", { 
    friction = 0.5, 
    bounce = 0.3 
    } )

local theGround2 = display.newImage( "./assets/sprites/land.png" )
theGround2.x = 1520
theGround2.y = display.contentHeight
theGround2.id = "the ground 2" -- notice I called this the same thing
physics.addBody( theGround2, "static", { 
    friction = 0.5, 
    bounce = 0.3 
    } )

--[[local robot = display.newImage( "./assets/sprites/robot.png" )
robot.x = 1520
robot.y = display.contentHeight - 1000
robot.id = "robot"
physics.addBody( robot, "dynamic", { 
    friction = 0.5, 
    bounce = 0.3 
    } )

local ninja = display.newImage( "./assets/sprites/ninja.png" )
ninja.x = display.contentCenterX - 200
ninja.y = display.contentCenterY
ninja.id = "ninja"
physics.addBody( ninja, "dynamic", { 
    density = 3.0, 
    friction = 0.5, 
    bounce = 0.3 
    } )
ninja.isFixedRotation = true
--]]

local leftArrow = display.newImage( "./assets/sprites/leftArrow.png" )
leftArrow.x = 40
leftArrow.y = display.contentHeight - 80
leftArrow.id = "left arrow"
leftArrow.alpha = 0.5

local rightArrow = display.newImage( "./assets/sprites/rightArrow.png" )
rightArrow.x = 260
rightArrow.y = display.contentHeight - 80
rightArrow.id = "right arrow"
rightArrow.alpha = 0.5

local jumpButton = display.newImage( "./assets/sprites/jumpButton.png" )
jumpButton.x = display.contentWidth - 80
jumpButton.y = display.contentHeight - 80
jumpButton.id = "jump button"
jumpButton.alpha = 0.5

local shootButton = display.newImage( "./assets/sprites/shootButton.png" )
shootButton.x = display.contentWidth - 250
shootButton.y = display.contentHeight - 80
shootButton.id = "shoot button"
shootButton.alpha = 0.5

centerX = display.contentWidth * .5
centerY = display.contentHeight * .5

local sheetOptionsIdle =
{
    width = 232,
    height = 439,
    numFrames = 10
}
local sheetIdleNinja = graphics.newImageSheet( "./assets/spritesheets/ninjaBoyIdle.png", sheetOptionsIdle )

local sheetOptionsWalk =
{
    width = 363,
    height = 458,
    numFrames = 10
}
local sheetWalkingNinja = graphics.newImageSheet( "./assets/spritesheets/ninjaBoyJumpRun.png", sheetOptionsWalk )

local sheetOptionsJump =
{
    width = 362,
    height = 483,
    numFrames = 10
}
local sheetJumpNinja = graphics.newImageSheet( "./assets/spritesheets/ninjaBoyJump.png", sheetOptionsJump )

local sheetOptionsThrow =
{
    width = 377,
    height = 451,
    numFrames = 10
}
local sheetThrowNinja = graphics.newImageSheet( "./assets/spritesheets/ninjaBoyThrow.png", sheetOptionsThrow )

-- sequences table
local sequence_data = {
    -- consecutive frames sequence
    {
        name = "idle",
        start = 1,
        count = 10,
        time = 800,
        loopCount = 0,
        sheet = sheetIdleNinja
    },
    {
        name = "walk",
        start = 1,
        count = 10,
        time = 1000,
        loopCount = 1,
        sheet = sheetWalkingNinja
    },
    {
        name = "throw",
        start = 1,
        count = 10,
        time = 1000,
        loopCount = 1,
        sheet = sheetThrowNinja
    },
    {
        name = "jump",
        start = 1,
        count = 10,
        time = 1000,
        loopCount = 1,
        sheet = sheetJumpNinja
    }
}

local sheetOptionsIdle2 =
{
    width = 567,
    height = 556,
    numFrames = 10
}
local sheetIdleRobot = graphics.newImageSheet( "./assets/spritesheets/robotIdle.png", sheetOptionsIdle2 )



local sheetOptionsDead2 =
{
    width = 562,
    height = 519,
    numFrames = 10
}
local sheetDiedRobot = graphics.newImageSheet( "./assets/spritesheets/robotDead.png", sheetOptionsDead2 )


-- sequences table
local sequence_data2 = {
    -- consecutive frames sequence
    {
        name = "idle",
        start = 1,
        count = 10,
        time = 800,
        loopCount = 0,
        sheet = sheetIdleRobot
    },
    {
        name = "dead",
        start = 1,
        count = 10,
        time = 1000,
        loopCount = 1,
        sheet = sheetDiedRobot
    }
    
}

local robot = display.newSprite( sheetIdleRobot, sequence_data2 )
robot.x = centerX + 650
robot.y = centerY
robot.id = "robot"
physics.addBody( robot, "dynamic", { 
    friction = 0.5, 
    bounce = 0.3 
    } )

robot:play()

local ninja = display.newSprite( sheetIdleNinja, sequence_data )
ninja.x = centerX - 650
ninja.y = centerY
ninja.id = "ninja"
physics.addBody( ninja, "dynamic", { 
    density = 3.0, 
    friction = 0.5, 
    bounce = 0.3 
    } )
ninja.isFixedRotation = true

ninja:play()


function rightArrow:touch( event )
    if ( event.phase == "ended" ) then
        -- move the character up
        transition.moveBy( ninja, { 
            x = 277, 
            y = 0, 
            time = 1000 
            } )
        ninja:setSequence( "walk" )
        ninja:play()
    end

    return true
end

function leftArrow:touch( event )
    if ( event.phase == "ended" ) then
        -- move the character up
        transition.moveBy( ninja, { 
            x = -277, 
            y = 0, 
            time = 1000 
            } )
        ninja:setSequence( "walk" )
        ninja:play()
    end

    return true
end


function jumpButton:touch( event )
    if ( event.phase == "ended" ) then
        -- make the character jump
        ninja:setLinearVelocity( 0, -750 )  

        ninja:setSequence( "jump" )
        ninja:play()
    
    end

    return true
end


function shootButton:touch( event )
    if ( event.phase == "began" ) then
        -- make a bullet appear
        local aSingleBullet = display.newImage( "./assets/sprites/Kunai.png" )
        aSingleBullet.x = ninja.x
        aSingleBullet.y = ninja.y
        physics.addBody( aSingleBullet, 'dynamic' )
        -- Make the object a "bullet" type object
        aSingleBullet.isBullet = true
        aSingleBullet.isFixedRotation = true
        aSingleBullet.gravityScale = 0
        aSingleBullet.id = "bullet"
        aSingleBullet:setLinearVelocity( 1500, 0 )

        table.insert(playerBullets,aSingleBullet)
        print("# of bullet: " .. tostring(#playerBullets))

        ninja:setSequence( "throw" )
        ninja:play()
    end

    return true
end

-- reset to idle 
local function resetToIdle (event)
    if event.phase == "ended" then
        ninja:setSequence("idle")
        ninja:play()
    end
end




-- if character falls off the end of the world, respawn back to where it came from
local function checkCharacterPosition( event )
    -- check every frame to see if character has fallen
    if ninja.y > display.contentHeight + 500 then
        ninja.x = display.contentCenterX - 200
        ninja.y = display.contentCenterY
    end
end

local function checkPlayerBulletsOutOfBounds()
	-- check if any bullets have gone off the screen
	local bulletCounter

    if #playerBullets > 0 then
        for bulletCounter = #playerBullets, 1 , -1 do
            if playerBullets[bulletCounter].x > display.contentWidth + 1000 then
                playerBullets[bulletCounter]:removeSelf()
                playerBullets[bulletCounter] = nil
                table.remove(playerBullets, bulletCounter)
                print("remove bullet")
            end
        end
    end
end


local function onCollision( event )
 
    if ( event.phase == "began" ) then
 
        local obj1 = event.object1
        local obj2 = event.object2
        local whereCollisonOccurredX = obj1.x
        local whereCollisonOccurredY = obj1.y

        if ( ( obj1.id == "robot" and obj2.id == "bullet" ) or
             ( obj1.id == "bullet" and obj2.id == "robot" ) ) then
            -- Remove both the laser and asteroid
            --display.remove( obj1 )
            --display.remove( obj2 )
 			
 			-- remove the bullet
 			local bulletCounter = nil
 			
            for bulletCounter = #playerBullets, 1, -1 do
                if ( playerBullets[bulletCounter] == obj1 or playerBullets[bulletCounter] == obj2 ) then
                    playerBullets[bulletCounter]:removeSelf()
                    playerBullets[bulletCounter] = nil
                    table.remove( playerBullets, bulletCounter )
                    break
                end
            end

            robot:setSequence( "dead" )
        	robot:play()

        	local function fadeOutRobot( event )
        		transition.fadeOut( robot, { time = 2000 }  )
        	end
        	timer.performWithDelay( 2000, fadeOutRobot )

        	local function removeRobot( event )
        		robot:removeSelf()
        		robot = nil
        	end
        	timer.performWithDelay( 4000, removeRobot )

            -- Increase score
            print ("you could increase a score here.")

            -- make an explosion sound effect
            local expolsionSound = audio.loadStream( "./assets/sounds/8bit_bomb_explosion.wav" )
            local explosionChannel = audio.play( expolsionSound )

            -- make an explosion happen
            -- Table of emitter parameters
			local emitterParams = {
			    startColorAlpha = 1,
			    startParticleSizeVariance = 250,
			    startColorGreen = 0,
			    yCoordFlipped = -1,
			    blendFuncSource = 770,
			    rotatePerSecondVariance = 153.95,
			    particleLifespan = 0.7237,
			    tangentialAcceleration = -1440.74,
			    finishColorBlue = 1,
			    finishColorGreen = 0,
			    blendFuncDestination = 1,
			    startParticleSize = 400.95,
			    startColorRed = 1,
			    textureFileName = "./assets/sprites/fire.png",
			    startColorVarianceAlpha = 1,
			    maxParticles = 256,
			    finishParticleSize = 540,
			    duration = 0.25,
			    finishColorRed = 1,
			    maxRadiusVariance = 77.25,
			    finishParticleSizeVariance = 250,
			    gravityy = -671.05,
			    speedVariance = 90.79,
			    tangentialAccelVariance = -420.11,
			    angleVariance = -142.62,
			    angle = -244.11
			}
			local emitter = display.newEmitter( emitterParams )
			emitter.x = whereCollisonOccurredX
			emitter.y = whereCollisonOccurredY

        end
    end
end

--------------------------------------------------------------------------------------------------
leftArrow:addEventListener( "touch", leftArrow )
rightArrow:addEventListener( "touch", rightArrow )

jumpButton:addEventListener( "touch", jumpButton )
shootButton:addEventListener( "touch", shootButton )

Runtime:addEventListener( "enterFrame", checkCharacterPosition )
Runtime:addEventListener( "enterFrame", checkPlayerBulletsOutOfBounds )
Runtime:addEventListener( "collision", onCollision )

ninja:addEventListener("sprite", resetToIdle)
