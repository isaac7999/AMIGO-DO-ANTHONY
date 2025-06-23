game.StarterGui:SetCore("SendNotification", {
    Title = "BY AMONG US",
    Text = "Aimbot ativado!";
    Duration = 5;
})

local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local aimRadius = 150 -- Ajuste o alcance da "circunferência invisível" aqui
local aimPart = "Head" -- Parte que vai mirar (cabeça)

local function IsFriend(player)
    local success, result = pcall(function()
        return LocalPlayer:IsFriendsWith(player.UserId)
    end)
    return success and result
end

local function GetClosestPlayer()
    local closestPlayer = nil
    local closestDistance = aimRadius

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild(aimPart) then
            local headPos, onScreen = Camera:WorldToViewportPoint(player.Character[aimPart].Position)
            local distanceFromCenter = (Vector2.new(headPos.X, headPos.Y) - Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)).Magnitude

            local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
            if humanoid and humanoid.Health > 0 and not IsFriend(player) and onScreen then
                if distanceFromCenter <= closestDistance then
                    closestDistance = distanceFromCenter
                    closestPlayer = player
                end
            end
        end
    end

    return closestPlayer
end

RunService.RenderStepped:Connect(function()
    local target = GetClosestPlayer()
    if target and target.Character and target.Character:FindFirstChild(aimPart) then
        local targetPos = target.Character[aimPart].Position
        Camera.CFrame = CFrame.new(Camera.CFrame.Position, targetPos)
    end
end)
