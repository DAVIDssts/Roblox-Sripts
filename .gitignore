-- Roblox Soft Lock-On with Circular FOV, Random Shooting at Head/Torso/Hands
-- Works with a Roblox executor like Synapse X or Fluxus

local player = game.Players.LocalPlayer
local camera = game.Workspace.CurrentCamera
local uis = game:GetService("UserInputService")
local runService = game:GetService("RunService")

local lockOn = false  -- Toggle state for lock-on
local fovRadius = 150  -- FOV circle radius
local target = nil  -- Locked enemy
local shootingInterval = 2  -- Time interval between shots (seconds)
local lastShotTime = 0  -- To track last time shot was fired

-- Function to find the closest enemy within the FOV circle
local function getClosestEnemy()
    local closest, shortestDist = nil, fovRadius
    for _, obj in pairs(game.Workspace:GetChildren()) do
        if obj:IsA("Model") and obj:FindFirstChild("Humanoid") and obj ~= player.Character then
            -- Find the humanoid root part or torso
            local torso = obj:FindFirstChild("HumanoidRootPart") or obj:FindFirstChild("Torso")
            if torso then
                local screenPos, onScreen = camera:WorldToViewportPoint(torso.Position)
                local dist = (Vector2.new(screenPos.X, screenPos.Y) - Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)).magnitude
                if onScreen and dist < shortestDist then
                    closest, shortestDist = obj, dist
                end
            end
        end
    end
    return closest
end

-- Function to toggle lock-on
local function toggleLockOn()
    if lockOn then
        lockOn = false
        target = nil
        print("Lock-On Disabled")
    else
        target = getClosestEnemy()
        if target then
            lockOn = true
            print("Lock-On Enabled")
        end
    end
end

-- Function to shoot at a random part (Head, Torso, Hands, etc.)
local function shootAtRandomPart()
    if target then
        local torso = target:FindFirstChild("HumanoidRootPart") or target:FindFirstChild("Torso")
        local head = target:FindFirstChild("Head")
        local leftHand = target:FindFirstChild("LeftHand")
        local rightHand = target:FindFirstChild("RightHand")
        
        -- Randomly choose a part to shoot at: Head, Torso, Left Hand, or Right Hand
        local partToShoot = nil
        local randomPart = math.random(1, 4)  -- Randomly choose 1, 2, 3, or 4
        if randomPart == 1 and head then
            partToShoot = head
        elseif randomPart == 2 and torso then
            partToShoot = torso
        elseif randomPart == 3 and leftHand then
            partToShoot = leftHand
        elseif randomPart == 4 and rightHand then
            partToShoot = rightHand
        end

        -- Shoot at the target part
        if partToShoot then
            print("Shot at: " .. partToShoot.Name)
        end
    end
end

-- Listen for "H" key press to toggle lock-on
uis.InputBegan:Connect(function(input, processed)
    if processed then return end
    if input.KeyCode == Enum.KeyCode.H then
        toggleLockOn()
    end
end)

-- Update loop for lock-on effect
runService.RenderStepped:Connect(function()
    -- Lock onto targets inside the FOV circle
    if lockOn then
        local closest = getClosestEnemy()
        if closest then
            local torso = closest:FindFirstChild("HumanoidRootPart") or closest:FindFirstChild("Torso")
            if torso then
                -- Immediately snap camera to look at the torso
                camera.CFrame = CFrame.lookAt(camera.CFrame.Position, torso.Position)
            end
        end

        -- Randomly shoot at the head/torso/hands at intervals
        if tick() - lastShotTime > shootingInterval then
            lastShotTime = tick()
            shootAtRandomPart()
        end
    end
end)
