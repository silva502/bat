-- decrypted from locker

 local Players = game:GetService("Players")

local RunService = game:GetService("RunService")

local UIS = game:GetService("UserInputService")

local TweenService = game:GetService("TweenService")

local LP = Players.LocalPlayer



-- // THEME (BLACK & GREY)

local C_BG         = Color3.fromRGB(15, 15, 15)

local C_HEADER     = Color3.fromRGB(20, 20, 20)

local C_CARD       = Color3.fromRGB(25, 25, 25)

local C_CARD_HOV   = Color3.fromRGB(35, 35, 35)

local C_BORDER     = Color3.fromRGB(45, 45, 45)

local C_BORDER_DIM = Color3.fromRGB(35, 35, 35)

local C_TEXT       = Color3.fromRGB(255, 255, 255)

local C_TEXT_SUB   = Color3.fromRGB(180, 180, 180)

local C_ACCENT     = Color3.fromRGB(255, 255, 255)

local C_RED        = Color3.fromRGB(235, 60, 60)

local C_GREEN      = Color3.fromRGB(60, 220, 120)

local C_CHIP_BG    = Color3.fromRGB(30, 30, 30)

local C_CHIP_TEXT  = Color3.fromRGB(200, 200, 200)

local C_PILL_OFF   = Color3.fromRGB(30, 30, 30)

local C_PILL_ON    = Color3.fromRGB(255, 255, 255)

local C_DOT_OFF    = Color3.fromRGB(60, 60, 60)

local C_DOT_ON     = Color3.fromRGB(0, 0, 0)



-- // STATE

local State = {

    autoBatToggled = false,

    hittingCooldown = false,

    guiVisible = true,

    binding = false,

}



local Keys = {

    autoBat = Enum.KeyCode.R,

    guiHide = Enum.KeyCode.LeftControl,

}



local h, hrp = nil, nil



-- // CLEANUP OLD GUI

for _, name in pairs({"GhostDsyncAutoBatGUI", "VulcanBatDesyncGUI", "MwvaneNewaBatDesyncGUI", "PhazeAutoBatDesyncGUI", "MwvaneDesyncAutoBatGUI"}) do

    local old = game:GetService("CoreGui"):FindFirstChild(name)

    if old then old:Destroy() end

end



-- // GUI

local gui = Instance.new("ScreenGui")

gui.Name="GhostDsyncAutoBatGUI"; gui.ResetOnSpawn=false; gui.DisplayOrder=10

gui.IgnoreGuiInset=true; gui.Parent=game:GetService("CoreGui")



local main = Instance.new("Frame",gui)

main.Name="Main"; main.Size=UDim2.new(0,260,0,160); main.Position=UDim2.new(0.5,-130,0.5,-80)

main.BackgroundColor3=C_BG; main.BorderSizePixel=0; main.Active=true; main.ClipsDescendants=false

Instance.new("UICorner",main).CornerRadius=UDim.new(0,12)

local mainStroke = Instance.new("UIStroke",main); mainStroke.Color=C_BORDER; mainStroke.Thickness=1



task.spawn(function() end)



-- // DRAGGABLE LOGIC

do

    local dragging, dragInput, dragStart, startPos = false, nil, nil, nil

    main.InputBegan:Connect(function(inp)

        if inp.UserInputType==Enum.UserInputType.MouseButton1 or inp.UserInputType==Enum.UserInputType.Touch then

            dragging=true; dragStart=inp.Position; startPos=main.Position

            inp.Changed:Connect(function()

                if inp.UserInputState==Enum.UserInputState.End then dragging=false end

            end)

        end

    end)

    main.InputChanged:Connect(function(inp)

        if inp.UserInputType==Enum.UserInputType.MouseMovement or inp.UserInputType==Enum.UserInputType.Touch then dragInput=inp end

    end)

    UIS.InputChanged:Connect(function(inp)

        if inp==dragInput and dragging then

            local dx = inp.Position.X - dragStart.X

            local dy = inp.Position.Y - dragStart.Y

            main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset+dx, startPos.Y.Scale, startPos.Y.Offset+dy)

        end

    end)

end



-- // HEADER

local titleLbl = Instance.new("TextLabel",main)

titleLbl.Size=UDim2.new(1,-40,0,22); titleLbl.Position=UDim2.new(0,12,0,8)

titleLbl.BackgroundTransparency=1; titleLbl.Text="Ghost DESYNC AUTO BAT"

titleLbl.TextColor3=C_ACCENT; titleLbl.Font=Enum.Font.GothamBold; titleLbl.TextSize=12

titleLbl.TextXAlignment=Enum.TextXAlignment.Left



local closeBtn = Instance.new("TextButton",main)

closeBtn.Size=UDim2.new(0,18,0,18); closeBtn.Position=UDim2.new(1,-26,0,10)

closeBtn.BackgroundColor3=C_BORDER; closeBtn.BorderSizePixel=0; closeBtn.Text="x"

closeBtn.TextColor3=Color3.new(1,1,1); closeBtn.Font=Enum.Font.GothamBold; closeBtn.TextSize=12

Instance.new("UICorner",closeBtn).CornerRadius=UDim.new(1,0)

closeBtn.MouseButton1Click:Connect(function() gui:Destroy() end)



-- // STATUS ROW

local statusRow = Instance.new("Frame",main)

statusRow.Size=UDim2.new(1,-20,0,30); statusRow.Position=UDim2.new(0,10,0,36)

statusRow.BackgroundColor3=C_CARD; statusRow.BorderSizePixel=0

Instance.new("UICorner",statusRow).CornerRadius=UDim.new(0,8)

local sRowStroke = Instance.new("UIStroke",statusRow); sRowStroke.Color=C_BORDER_DIM; sRowStroke.Thickness=1



local statusKey = Instance.new("TextLabel",statusRow)

statusKey.Size=UDim2.new(0.5,0,1,0); statusKey.Position=UDim2.new(0,12,0,0)

statusKey.BackgroundTransparency=1; statusKey.Text="Status"

statusKey.TextColor3=C_TEXT; statusKey.Font=Enum.Font.GothamMedium; statusKey.TextSize=12

statusKey.TextXAlignment=Enum.TextXAlignment.Left



local statusVal = Instance.new("TextLabel",statusRow)

statusVal.Size=UDim2.new(0.5,-12,1,0); statusVal.Position=UDim2.new(0.5,0,0,0)

statusVal.BackgroundTransparency=1; statusVal.Text="OFF"

statusVal.TextColor3=C_RED; statusVal.Font=Enum.Font.GothamBold; statusVal.TextSize=12

statusVal.TextXAlignment=Enum.TextXAlignment.Right



-- // AUTO BAT ROW

local row = Instance.new("Frame",main)

row.Size=UDim2.new(1,-20,0,30); row.Position=UDim2.new(0,10,0,72)

row.BackgroundColor3=C_CARD; row.BorderSizePixel=0

Instance.new("UICorner",row).CornerRadius=UDim.new(0,8)

local rowStroke = Instance.new("UIStroke",row); rowStroke.Color=C_BORDER_DIM; rowStroke.Thickness=1



local rowLbl = Instance.new("TextLabel",row)

rowLbl.Size=UDim2.new(0,80,1,0); rowLbl.Position=UDim2.new(0,12,0,0)

rowLbl.BackgroundTransparency=1; rowLbl.Text="Auto Bat"

rowLbl.TextColor3=C_TEXT; rowLbl.Font=Enum.Font.GothamMedium; rowLbl.TextSize=12

rowLbl.TextXAlignment=Enum.TextXAlignment.Left



-- Keybind chip

local keyBox = Instance.new("Frame",row)

keyBox.Size=UDim2.new(0,40,0,20); keyBox.Position=UDim2.new(1,-94,0.5,-10)

keyBox.BackgroundColor3=C_CHIP_BG; keyBox.BorderSizePixel=0

Instance.new("UICorner",keyBox).CornerRadius=UDim.new(0,6)

local keyStroke = Instance.new("UIStroke",keyBox); keyStroke.Color=C_BORDER_DIM; keyStroke.Thickness=1

local keyLbl = Instance.new("TextLabel",keyBox)

keyLbl.Size=UDim2.new(1,0,1,0); keyLbl.BackgroundTransparency=1; keyLbl.Text="R"

keyLbl.TextColor3=C_CHIP_TEXT; keyLbl.Font=Enum.Font.GothamBold; keyLbl.TextSize=11



local bindBtn = Instance.new("TextButton", keyBox)

bindBtn.Size = UDim2.new(1,0,1,0); bindBtn.BackgroundTransparency = 1; bindBtn.Text = ""; bindBtn.ZIndex = 5

bindBtn.MouseButton1Click:Connect(function()

    State.binding = true

    keyLbl.Text = "..."

    keyStroke.Color = C_ACCENT

end)



-- Toggle Pill

local pillBg = Instance.new("Frame",row)

pillBg.Size=UDim2.new(0,38,0,20); pillBg.Position=UDim2.new(1,-46,0.5,-10)

pillBg.BackgroundColor3=C_PILL_OFF; pillBg.BorderSizePixel=0

Instance.new("UICorner",pillBg).CornerRadius=UDim.new(1,0)

local pStroke = Instance.new("UIStroke",pillBg); pStroke.Color=C_BORDER; pStroke.Thickness=1

local dot = Instance.new("Frame",pillBg)

dot.Size=UDim2.new(0,14,0,14); dot.Position=UDim2.new(0,3,0.5,-7)

dot.BackgroundColor3=C_DOT_OFF; dot.BorderSizePixel=0

Instance.new("UICorner",dot).CornerRadius=UDim.new(1,0)



local pillClk = Instance.new("TextButton", pillBg)

pillClk.Size=UDim2.new(1,0,1,0); pillClk.BackgroundTransparency=1; pillClk.Text=""; pillClk.ZIndex=5



-- // BIG BUTTON

local bigBtn = Instance.new("TextButton",main)

bigBtn.Size=UDim2.new(1,-20,0,38); bigBtn.Position=UDim2.new(0,10,1,-48)

bigBtn.BackgroundColor3=C_CARD; bigBtn.BorderSizePixel=0; bigBtn.AutoButtonColor=false

bigBtn.Text="CLICK TO TOGGLE AUTO BAT"

bigBtn.TextColor3=C_TEXT; bigBtn.Font=Enum.Font.GothamBold; bigBtn.TextSize=12

Instance.new("UICorner",bigBtn).CornerRadius=UDim.new(0,8)

local bigStroke = Instance.new("UIStroke",bigBtn); bigStroke.Color=C_BORDER_DIM; bigStroke.Thickness=1



-- // VISUAL UPDATE

local function setToggleVisual(on)

    TweenService:Create(pillBg,TweenInfo.new(0.2),{BackgroundColor3=on and C_PILL_ON or C_PILL_OFF}):Play()

    TweenService:Create(dot,TweenInfo.new(0.2,Enum.EasingStyle.Back),{

        Position=on and UDim2.new(1,-17,0.5,-7) or UDim2.new(0,3,0.5,-7),

        BackgroundColor3=on and C_DOT_ON or C_DOT_OFF

    }):Play()

    statusVal.Text = on and "ON" or "OFF"

    statusVal.TextColor3 = on and C_GREEN or C_RED

end



local function toggleAuto()

    State.autoBatToggled = not State.autoBatToggled

    setToggleVisual(State.autoBatToggled)

end



pillClk.MouseButton1Click:Connect(toggleAuto)

bigBtn.MouseButton1Click:Connect(toggleAuto)



bigBtn.MouseEnter:Connect(function()

    TweenService:Create(bigBtn,TweenInfo.new(0.15),{BackgroundColor3=C_CARD_HOV}):Play()

end)

bigBtn.MouseLeave:Connect(function()

    TweenService:Create(bigBtn,TweenInfo.new(0.15),{BackgroundColor3=C_CARD}):Play()

end)

row.MouseEnter:Connect(function()

    TweenService:Create(row,TweenInfo.new(0.15),{BackgroundColor3=C_CARD_HOV}):Play()

end)

row.MouseLeave:Connect(function()

    TweenService:Create(row,TweenInfo.new(0.15),{BackgroundColor3=C_CARD}):Play()

end)



-- // BAT LOGIC

local function getBat()

    local char=LP.Character; if not char then return nil end

    local tool=char:FindFirstChild("Bat"); if tool then return tool end

    local bp2=LP:FindFirstChild("Backpack")

    if bp2 then tool=bp2:FindFirstChild("Bat"); if tool then tool.Parent=char; return tool end end

    return nil

end



local function tryHitBat()

    if State.hittingCooldown then return end; State.hittingCooldown=true

    pcall(function()

        local bat=getBat(); if bat then

            bat:Activate(); local ev=bat:FindFirstChildWhichIsA("RemoteEvent")

            if ev then ev:FireServer() end

        end

    end)

    task.delay(0.08, function() State.hittingCooldown=false end)

end



local function getClosestPlayer()

    if not hrp then return nil,math.huge end

    local cp,cd=nil,math.huge

    for _,p in pairs(Players:GetPlayers()) do

        if p~=LP and p.Character then

            local tr=p.Character:FindFirstChild("HumanoidRootPart")

            if tr then local d=(hrp.Position-tr.Position).Magnitude; if d<cd then cd=d; cp=p end end

        end

    end

    return cp,cd

end



-- // CHARACTER SETUP

local function setupChar(char)

    task.wait(0.1)

    h=char:WaitForChild("Humanoid",5); hrp=char:WaitForChild("HumanoidRootPart",5)

    if not h or not hrp then return end

end



LP.CharacterAdded:Connect(setupChar)

if LP.Character then task.spawn(function() setupChar(LP.Character) end) end



-- // BAT AIMBOT HEARTBEAT

RunService.Heartbeat:Connect(function()

    if not (State.autoBatToggled and h and hrp) then return end

    local target,dist=getClosestPlayer()

    if target and target.Character then

        local tr=target.Character:FindFirstChild("HumanoidRootPart")

        if tr then

            if sethiddenproperty then

                sethiddenproperty(hrp, "PhysicsRepRootPart", tr)

            end

            local targetPos = tr.Position + Vector3.new(0, 0.9, 0)

            if (hrp.Position - targetPos).Magnitude > 8 then

                hrp.CFrame = CFrame.new(targetPos)

            end

            local cam = workspace.CurrentCamera

            cam.CFrame = CFrame.new(cam.CFrame.Position, tr.Position)

            tryHitBat()

        end

    end

end)



local function shortKey(name)

    local s = name:gsub("Left","L"):gsub("Right","R"):gsub("Control","Ctrl"):gsub("Shift","Shft"):gsub("Alt","Alt"):gsub("MouseButton","MB"):gsub("Button","Btn")

    return s

end



-- // KEYBIND HANDLER

UIS.InputBegan:Connect(function(inp, gp)

    if gp and not State.binding then return end

    if inp.UserInputType~=Enum.UserInputType.Keyboard then return end

    local kc=inp.KeyCode



    if State.binding then

        if kc ~= Enum.KeyCode.Unknown and kc ~= Keys.guiHide then

            Keys.autoBat = kc

            keyLbl.Text = shortKey(kc.Name)

            State.binding = false

            keyStroke.Color = C_BORDER_DIM

        end

        return

    end



    if kc==Keys.autoBat then

        toggleAuto()

    elseif kc==Keys.guiHide then

        State.guiVisible=not State.guiVisible

        main.Visible=State.guiVisible

    end

end)

